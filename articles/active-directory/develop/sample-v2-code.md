---
title: Codevoorbeelden voor microsoft-identiteitsplatform
description: Biedt een index van beschikbare Microsoft-identiteitsplatform (v2.0-eindpunt) codesamples, geordend op scenario.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5596f5f40e293a7bd11ac6a5f900e05c0b8d3b0e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883131"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft identity platform code samples (v2.0 endpoint)

U het identiteitsplatform van Microsoft gebruiken om:

- Voeg verificatie en autorisatie toe aan uw webtoepassingen en web-API's.
- Een toegangstoken vereisen om toegang te krijgen tot een beveiligde web-API.

In dit artikel worden koppelingen naar voorbeelden voor het eindpunt van het Microsoft-identiteitsplatform kort beschreven en u voorzien. Deze voorbeelden laten u zien hoe het wordt gedaan en bieden ook codefragmenten die u in uw toepassingen gebruiken. Op de voorbeeldpagina van de code vindt u gedetailleerde leesthema's die helpen bij vereisten, installatie en installatie. Opmerkingen in de code helpen u inzicht te krijgen in de kritieke secties.

> [!NOTE]
> Zie [Azure AD-codesamples (v1.0-eindpunt)](../azuread-dev/sample-v1-code.md)als u geïnteresseerd bent in v1.0-voorbeelden.

Zie [App-typen voor het eindpunt van het Microsoft-identiteitsplatform voor](v2-app-types.md)het basisscenario voor elk voorbeeldtype.

U ook bijdragen aan de monsters op GitHub. Zie [Microsoft Azure Active Directory-voorbeelden en -documentatie](https://github.com/Azure-Samples?page=3&query=active-directory)voor meer informatie.

## <a name="single-page-applications"></a>Toepassingen met één pagina

Deze voorbeelden laten zien hoe u een toepassing van één pagina schrijven die is beveiligd met het Microsoft-identiteitsplatform. Deze monsters maken gebruik van een van de smaken van MSAL.js.

| Platform | Beschrijving | Koppeling |
| -------- | --------------------- | -------- |
| ![Deze afbeelding toont het](media/sample-v2-code/logo_js.png) JavaScript-logo [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Oproepen in Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Deze afbeelding toont het](media/sample-v2-code/logo_js.png) JavaScript-logo [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Gesprekken B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Deze afbeelding toont het](media/sample-v2-code/logo_js.png) JavaScript-logo [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Oproepen eigen web-API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Deze afbeelding toont](media/sample-v2-code/logo_angular.png) het Hoekige logo [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Oproepen in Microsoft Graph  | [active-directory-javascript-singlepageapp-hoekig](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Deze afbeelding toont](media/sample-v2-code/logo_angular.png) het Hoekige logo [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Gesprekken B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Webtoepassingen

De volgende voorbeelden illustreren webtoepassingen die gebruikers aanmelden. Sommige voorbeelden tonen ook de toepassing die Microsoft Graph aanroept, of uw eigen web-API met de identiteit van de gebruiker.

| Platform | Alleen aan-het eind van de gebruikers | Aanmeldingen bij gebruikers en belt Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Deze afbeelding toont het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [ASP.NET Core WebApp-aan-/in-gebruikerszelfstudie](https://aka.ms/aspnetcore-webapp-sign-in) | Hetzelfde voorbeeld in de [ASP.NET Core Web App noemt Microsoft Graph fase](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Deze afbeelding toont het ASP.NET logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET Snelstart](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Deze afbeelding toont het Java-logo](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Deze afbeelding toont het Python-logo](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Deze afbeelding toont het Ruby-logo](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop- en mobiele openbare client-apps

In de volgende voorbeelden worden openbare clienttoepassingen (desktop- of mobiele toepassingen) weergegeven die toegang hebben tot de Microsoft Graph-API of uw eigen web-API in naam van een gebruiker. Al deze clienttoepassingen maken gebruik van Microsoft Authentication Library (MSAL).

| Clienttoepassing | Platform | Stroom/subsidie | Oproepen in Microsoft Graph | Roept een ASP.NET Core 2.0-web-API aan |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Bureaublad (WPF)      | ![Deze afbeelding toont het .NET/C#-logo](media/sample-v2-code/logo_NET.png) | [interactief](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Bureaublad (console)   | ![Deze afbeelding toont het .NET/C#-logo (Bureaublad)](media/sample-v2-code/logo_NET.png) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Bureaublad (console)   | ![Deze afbeelding toont het Java-logo](media/sample-v2-code/logo_java.png) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureaublad (console)   | ![Deze afbeelding toont het .NET/C#-logo (Bureaublad)](media/sample-v2-code/logo_NETcore.png) | [Gebruikersnaam/wachtwoord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Bureaublad (console) met WAM  | ![Deze afbeelding toont het .NET/C#-logo (Bureaublad)](media/sample-v2-code/logo_NETcore.png) | [interactief met WAM](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Bureaublad (console)   | ![Deze afbeelding toont het Java-logo](media/sample-v2-code/logo_java.png) | [Gebruikersnaam/wachtwoord](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureaublad (console)   | ![Deze afbeelding toont het Python-logo](media/sample-v2-code/logo_python.png) | [Gebruikersnaam/wachtwoord](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobiel (Android, iOS, UWP)   | ![Deze afbeelding toont het .NET/C#(Xamarin) logo](media/sample-v2-code/logo_xamarin.png) | [interactief](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobiel (iOS)       | ![Deze afbeelding toont iOS/Objective-C of Swift](media/sample-v2-code/logo_iOS.png) | [interactief](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Bureaublad (macOS)       | macOS | [interactief](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobiel (Android-Java)   | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo_Android.png) | [interactief](msal-authentication-flows.md#interactive) |  [Android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobiel (Android-Kotlin)   | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo_Android.png) | [interactief](msal-authentication-flows.md#interactive) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-toepassingen

In de volgende voorbeelden wordt een toepassing weergegeven die toegang heeft tot de Microsoft Graph API met een eigen identiteit (zonder gebruiker).

| Clienttoepassing | Platform | Stroom/subsidie | Oproepen in Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Deze afbeelding toont het .NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web-app | ![Deze afbeelding toont het ASP.NET logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Deze afbeelding toont het Java-logo](media/sample-v2-code/logo_java.png) | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Deze afbeelding toont het Python-logo](media/sample-v2-code/logo_python.png) | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Headless toepassingen

In het volgende voorbeeld wordt een openbare clienttoepassing weergegeven die wordt uitgevoerd op een apparaat zonder webbrowser. De app kan een command-line tool zijn, een app die draait op Linux of Mac of een IoT-toepassing. Het voorbeeld bevat een app die toegang heeft tot de Microsoft Graph API, op naam van een gebruiker die zich interactief aanmeldt op een ander apparaat (zoals een mobiele telefoon). Deze clienttoepassing maakt gebruik van Microsoft Authentication Library (MSAL).

| Clienttoepassing | Platform | Stroom/subsidie | Oproepen in Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Bureaublad (console)   | ![Deze afbeelding toont het .NET/C#-logo (Bureaublad)](media/sample-v2-code/logo_NETcore.png) | [Apparaatcodestroom](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Bureaublad (console)   | ![Deze afbeelding toont het Java-logo](media/sample-v2-code/logo_java.png) | [Apparaatcodestroom](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Bureaublad (console)   | ![Deze afbeelding toont het Python-logo](media/sample-v2-code/logo_python.png) | [Apparaatcodestroom](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Web-API's

In de volgende voorbeelden wordt uitgelegd hoe u een web-API beveiligen met het eindpunt van het Microsoft-identiteitsplatform en hoe u een downstream-API uit de web-API aanroepen.

| Platform | Voorbeeld |
| -------- | ------------------- |
| ![Deze afbeelding toont het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core web API (service) van [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Deze afbeelding toont het ASP.NET logo](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web API (service) van [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Deze afbeelding toont het Java-logo](media/sample-v2-code/logo_java.png) | Web API (service) van [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Deze afbeelding toont het Node.js-logo](media/sample-v2-code/logo_nodejs.png) | Web API (service) van [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Deze afbeelding toont het Node.js-logo](media/sample-v2-code/logo_nodejs.png) | B2C Web API (service) van [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure-functies als web-API's

In de volgende voorbeelden wordt uitgelegd hoe u een Azure-functie beveiligen met HttpTrigger en hoe u een web-API blootstellen met het eindpunt van het Microsoft-identiteitsplatform en hoe u een downstream-API vanuit de web-API aanroepen.

| Platform | Voorbeeld |
| -------- | ------------------- |
| ![Deze afbeelding toont het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core web API (service) Azure-functie van [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Deze afbeelding toont het Node.js-logo](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web API (service) van [NodeJS en passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Deze afbeelding toont het Python-logo](media/sample-v2-code/logo_python.png)</p>Python | Web API (service) van [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Deze afbeelding toont het Node.js-logo](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web API (service) van [NodeJS en passport-azure-ad gebruiken namens](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Andere Voorbeelden van Microsoft Graph

Zie Voorbeelden van [Microsoft Graph Community-&-zelfstudies](https://github.com/microsoftgraph/msgraph-community-samples)voor meer informatie over [voorbeelden](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) en zelfstudies die verschillende gebruikspatronen voor de Microsoft Graph-API demonstreren, waaronder verificatie met Azure AD.

## <a name="see-also"></a>Zie ook

- [Handleiding voor Azure Active Directory -ontwikkelaars (v1.0)](../azuread-dev/v1-overview.md)
- [Conceptueel en naslagwerk van Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
