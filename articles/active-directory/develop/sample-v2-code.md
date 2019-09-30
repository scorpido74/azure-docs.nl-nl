---
title: Code voorbeelden voor het micro soft Identity platform | Microsoft Docs
description: Biedt een index van beschik bare code voorbeelden van het micro soft Identity platform (v 2.0-eind punt), geordend op scenario.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86f9b6a6139f329a27914eff7a61d0f59c7f48ad
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678075"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Micro soft Identity platform code samples (v 2.0-eind punt)

U kunt micro soft Identity platform gebruiken voor het volgende:

- Voeg verificatie en autorisatie toe aan uw webtoepassingen en Web-Api's.
- Een toegangs token vereisen om toegang te krijgen tot een beveiligde web-API.

In dit artikel wordt kort beschreven en vindt u koppelingen naar voor beelden voor het micro soft Identity platform-eind punt. In deze voor beelden ziet u hoe deze worden uitgevoerd, en kunt u ook code fragmenten opgeven die u in uw toepassingen gebruikt. Op de voorbeeld pagina code vindt u gedetailleerde Leesmij-onderwerpen die u helpen bij vereisten, installatie en installatie. Opmerkingen in de code helpen u de kritieke secties te begrijpen.

> [!NOTE]
> Als u geïnteresseerd bent in v 1.0-voor beelden, raadpleegt u [Azure ad-code voorbeelden (v 1.0-eind punt)](sample-v1-code.md).

Zie [app-typen voor het micro soft Identity platform-eind punt](v2-app-types.md)voor meer informatie over het basis scenario voor elk type sample.

U kunt ook bijdragen aan de voor beelden op GitHub. Zie Microsoft Azure Active Directory-voor [beelden en-documentatie](https://github.com/Azure-Samples?page=3&query=active-directory)voor meer informatie.

## <a name="single-page-applications"></a>Toepassingen met één pagina

In deze voor beelden ziet u hoe u een toepassing met één pagina schrijft die is beveiligd met micro soft Identity platform. In deze voor beelden wordt een van de MSAL. js gebruikt.

| Platform | Description | Koppelen |
| -------- | --------------------- | -------- |
| ![Deze afbeelding toont het Java script](media/sample-v2-code/logo_js.png) [-logo java script (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Aanroepen Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Deze afbeelding toont het Java script](media/sample-v2-code/logo_js.png) [-logo java script (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Roept B2C aan |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Deze afbeelding toont het Java script](media/sample-v2-code/logo_js.png) [-logo java script (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Hiermee wordt een eigen web-API aangeroepen |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Deze afbeelding toont het hoek js-](media/sample-v2-code/logo_angular.png) logo [Java script (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Aanroepen Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![In deze afbeelding wordt het hoek](media/sample-v2-code/logo_angular.png) logo [Java script (MSAL hoek)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) weer gegeven| Aanroepen Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Webtoepassingen

De volgende voor beelden illustreren webtoepassingen die aanmelden bij gebruikers. Sommige voor beelden demonstreren ook de toepassing die Microsoft Graph aanroept of uw eigen web-API met de identiteit van de gebruiker.

| Platform | Alleen tekenen in gebruikers | Ondertekent gebruikers en aanroepen Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Deze afbeelding toont het ASP.NET Core logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | [Zelf studie voor gebruikers van de webASP.NET Core WebApp-aanmelding](https://aka.ms/aspnetcore-webapp-sign-in) | Hetzelfde voor beeld in de [ASP.net core web-app aanroepen Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fase |
| ![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET Quick Start](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp): een MSAL4J-web-app die micro soft Graph aanroept |
| ![Deze afbeelding toont het node. js-logo](media/sample-v2-code/logo_nodejs.png)  |                   | [Node. js Quick Start](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Deze afbeelding toont het ruby-logo](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop-en mobiele open bare client-apps

De volgende voor beelden tonen open bare client toepassingen (desktop-of mobiele toepassingen) die toegang hebben tot de Microsoft Graph-API of uw eigen web-API in de naam van een gebruiker. Al deze client toepassingen gebruiken micro soft Authentication Library (MSAL).

| Client toepassing | Platform | Stroom/verlenen | Aanroepen Microsoft Graph | Hiermee wordt een ASP.NET Core 2,0-Web-API aangeroepen |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Bureau blad (WPF)      | ![In deze afbeelding wordt het .NETC# /logo weer gegeven](media/sample-v2-code/logo_NET.png) | [interactief](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Bureau blad (console)   | ![In deze afbeelding wordt het .NETC# /(Desktop)-logo weer gegeven](media/sample-v2-code/logo_NET.png) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Bureau blad (console)   | ![In deze afbeelding wordt het .NETC# /(Desktop)-logo weer gegeven](media/sample-v2-code/logo_NETcore.png) | [Gebruikersnaam en wachtwoord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobiel (Android, iOS, UWP)   | ![In deze afbeelding wordt het .NETC# /(Xamarin)-logo weer gegeven](media/sample-v2-code/logo_xamarin.png) | [interactief](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobiel (iOS)       | ![Deze afbeelding toont iOS/objectief-C of SWIFT](media/sample-v2-code/logo_iOS.png) | [interactief](msal-authentication-flows.md#interactive) |[IOS-Swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Bureau blad (macOS)       | macOS | [interactief](msal-authentication-flows.md#interactive) |[macOS-Swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobiel (Android-Java)   | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo_Android.png) | [interactief](msal-authentication-flows.md#interactive) |  [Android-java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobiel (Android-Kotlin)   | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo_Android.png) | [interactief](msal-authentication-flows.md#interactive) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-toepassingen

In de volgende voor beelden ziet u een toepassing die toegang heeft tot de Microsoft Graph-API met een eigen identiteit (zonder gebruiker).

| Client toepassing | Platform | Stroom/verlenen | Aanroepen Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![In deze afbeelding wordt het .NET core-logo weer gegeven](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Client referenties](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web-app | ![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Client referenties](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Headless toepassingen

In het volgende voor beeld ziet u een open bare client toepassing die wordt uitgevoerd op een apparaat zonder webbrowser. De app kan een opdracht regel programma zijn, een app die wordt uitgevoerd op Linux of Mac of een IoT-toepassing. Het voor beeld bevat een app die toegang heeft tot de Microsoft Graph-API, in de naam van een gebruiker die zich interactief aanmeldt op een ander apparaat (zoals een mobiele telefoon). Deze client toepassing maakt gebruik van micro soft Authentication Library (MSAL).

| Client toepassing | Platform | Stroom/verlenen | Aanroepen Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Bureau blad (console)   | ![In deze afbeelding wordt het .NETC# /(Desktop)-logo weer gegeven](media/sample-v2-code/logo_NETcore.png) | [Toestel code stroom](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Web-API's

De volgende voor beelden laten zien hoe u een web-API kunt beveiligen met het micro soft Identity platform-eind punt en hoe u een downstream API aanroept vanuit de Web-API.

| Platform | Voorbeeld |
| -------- | ------------------- |
| ![Deze afbeelding toont het ASP.NET Core logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2,2 | ASP.NET Core Web-API (Service) van [DotNet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web-API (Service) van [MS-Identity-ASPNET-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Andere Microsoft Graph-voor beelden

Zie [Microsoft Graph Community-voor beelden & zelf studies](https://github.com/microsoftgraph/msgraph-community-samples)voor meer informatie over de voor [beelden](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) en zelf studies die verschillende gebruiks patronen voor de Microsoft Graph-API tonen, inclusief verificatie met Azure AD.

## <a name="see-also"></a>Zie ook

- [Hand leiding voor ontwikkel aars voor Azure Active Directory (v 1.0)](v1-overview.md)
- [Concepten en naslag informatie over Azure AD Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API helper-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
