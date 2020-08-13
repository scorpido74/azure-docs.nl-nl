---
title: Codevoorbeelden voor Microsoft identity platform
description: Biedt een index van beschikbare codevoorbeelden voor Microsoft identity platform (v2.0-eindpunt), geordend op scenario.
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
ms.openlocfilehash: 142037366e9309f8087a2e42fa46cd0b4137f6b8
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119399"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Codevoorbeelden voor Microsoft identity platform (v2.0-eindpunt)

U kunt Microsoft identity platform gebruiken voor het volgende:

- Verificatie en autorisatie toevoegen aan uw webtoepassingen en web-API's.
- Een toegangstoken vereisen voor toegang tot een beveiligde web-API.

Dit artikel bevat een korte beschrijving van en koppelingen naar voorbeelden voor het Microsoft identity platform-eindpunt. Deze voorbeelden laten zien hoe het werkt en bevatten ook codefragmenten die u kunt gebruiken in uw toepassingen. Op de pagina met codevoorbeelden vindt u gedetailleerde leesmij-onderwerpen om u te helpen bij de vereisten, installatie en configuratie. Opmerkingen in de code helpen u de kritieke secties te begrijpen.

> [!NOTE]
> Als u geïnteresseerd bent in v1.0-voorbeelden, raadpleegt u [Azure AD-codevoorbeelden (v1.0-eindpunt)](../azuread-dev/sample-v1-code.md).

Zie [App-typen voor het Microsoft identity platform-eindpunt](v2-app-types.md) voor meer informatie over het basisscenario voor elk type voorbeeld.

U kunt ook bijdragen aan de voorbeelden op GitHub. Zie [Voorbeelden en documentatie voor Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory) voor meer informatie.

## <a name="single-page-applications"></a>Toepassingen met één pagina

In deze voorbeelden ziet u hoe u een toepassing met één pagina (SPA) schrijft die is beveiligd met Microsoft identity platform. In deze voorbeelden wordt een van de varianten van MSAL.js gebruikt.

| Platform | Beschrijving | Koppeling |
| -------- | --------------------- | -------- |
| ![In deze afbeelding ziet u het JavaScript-logo](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA roept Microsoft Graph aan |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![In deze afbeelding ziet u het JavaScript-logo](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA roept Microsoft Graph aan met behulp van een verificatiecodestroom met PKCE |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![In deze afbeelding ziet u het JavaScript-logo](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA roept B2C aan |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![In deze afbeelding ziet u het Angular-logo](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA roept Microsoft Graph aan  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![In deze afbeelding ziet u het Angular-logo](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA roept aangepaste web-API aan | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![In deze afbeelding ziet u het Angular-logo](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA roept B2C aan |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![In deze afbeelding ziet u het React-logo](media/sample-v2-code/logo_react.png) [React (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA roept aangepaste web-API aan, die op zijn beurt Microsoft Graph aanroept  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![In deze afbeelding ziet u het Angular-logo](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA roept aangepaste web-API met app-rollen en beveiligingsgroepen aan |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Webtoepassingen

De volgende voorbeelden illustreren webtoepassingen waarmee gebruikers worden aangemeld. Sommige voorbeelden demonstreren ook de toepassing waarmee Microsoft Graph wordt aangeroepen, of uw eigen web-API met de identiteit van de gebruiker.

| Platform | Meldt alleen gebruikers aan | Meldt gebruikers aan en roept Microsoft Graph aan |
| -------- | ------------------- | --------------------------------- |
| ![In deze afbeelding ziet u het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Zelfstudie voor gebruikersaanmelding met ASP.NET Core-web-app](https://aka.ms/aspnetcore-webapp-sign-in) | Hetzelfde voorbeeld in de fase [ASP.NET Core web-app roept Microsoft Graph aan](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![In deze afbeelding ziet u het ASP.NET-logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET-quickstart](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![In deze afbeelding ziet u het Python-logo](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![In deze afbeelding ziet u het Ruby-logo](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Openbare client-apps voor bureaublad en mobiel

De volgende voorbeelden tonen openbare clienttoepassingen (bureaublad- of mobiele toepassingen) die de Microsoft Graph-API aanroepen, of uw eigen web-API namens een gebruiker. Met uitzondering van het voorbeeld *Bureaublad (console) met WAM*, maken al deze clienttoepassingen gebruik van Microsoft Authentication Library (MSAL).

| Clienttoepassing | Platform | Stroom/toekenning | Roept Microsoft Graph aan | Roept een ASP.NET Core-web-API aan |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Bureaublad (WPF)      | ![In deze afbeelding ziet u het .NET/C#-logo](media/sample-v2-code/logo_NET.png) | [Autorisatiecode](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Bureaublad (console)   | ![In deze afbeelding ziet u het .NET/C#-logo (Desktop)](media/sample-v2-code/logo_NET.png) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Bureaublad (console)   | ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo_java.png) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureaublad (console)   | ![In deze afbeelding ziet u het .NET/C#-logo (bureaublad)](media/sample-v2-code/logo_NETcore.png) | [Gebruikersnaam en wachtwoord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Bureaublad (console) met WAM  | ![In deze afbeelding ziet u het .NET/C#-logo (bureaublad)](media/sample-v2-code/logo_NETcore.png) | Interactief met [Web Account Manager](/windows/uwp/security/web-account-manager) (WAM) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Bureaublad (console)   | ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo_java.png) | [Gebruikersnaam en wachtwoord](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureaublad (console)   | ![In deze afbeelding ziet u het Python-logo](media/sample-v2-code/logo_python.png) | [Gebruikersnaam en wachtwoord](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobiel (Android, iOS, UWP)   | ![In deze afbeelding ziet u het .NET/C#-logo (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Autorisatiecode](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobiel (iOS)       | ![In deze afbeelding ziet u iOS/Objective-C of Swift](media/sample-v2-code/logo_iOS.png) | [Autorisatiecode](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Bureaublad (macOS)       | macOS | [Autorisatiecode](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobiel (Android-Java)   | ![In deze afbeelding ziet u het Android-logo](media/sample-v2-code/logo_Android.png) | [Autorisatiecode](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobiel (Android-Kotlin)   | ![In deze afbeelding ziet u het Android-logo](media/sample-v2-code/logo_Android.png) | [Autorisatiecode](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemontoepassingen

In de volgende voorbeelden ziet u een toepassing die de Microsoft Graph-API aanroept met de eigen identiteit (zonder gebruiker).

| Clienttoepassing | Platform | Stroom/Toekenning | Roept Microsoft Graph aan |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![In deze afbeelding ziet u het .NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web-app | ![In deze afbeelding ziet u het ASP.NET-logo](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo_java.png) | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![In deze afbeelding ziet u het Python-logo](media/sample-v2-code/logo_python.png) | [Clientreferenties](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Headless-toepassingen

In het volgende voorbeeld ziet u een openbare clienttoepassing die wordt uitgevoerd op een apparaat zonder webbrowser. De app kan een opdrachtregelprogramma, een app die wordt uitgevoerd op Linux of Mac, of een IoT-toepassing zijn. Het voorbeeld bevat een app die de Microsoft Graph-API aanroept, namens een gebruiker die zich interactief aanmeldt op een ander apparaat (zoals een mobiele telefoon). Deze clienttoepassing maakt gebruik van Microsoft Authentication Library (MSAL).

| Clienttoepassing | Platform | Stroom/Toekenning | Roept Microsoft Graph aan |
| ------------------ | -------- |  ----------| ---------- |
| Bureaublad (console)   | ![In deze afbeelding ziet u het .NET/C#-logo (bureaublad)](media/sample-v2-code/logo_NETcore.png) | [Stroom voor apparaatcode](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Bureaublad (console)   | ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo_java.png) | [Stroom voor apparaatcode](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Bureaublad (console)   | ![In deze afbeelding ziet u het Python-logo](media/sample-v2-code/logo_python.png) | [Stroom voor apparaatcode](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>SaaS-toepassingen voor meerdere tenants

De volgende voorbeelden laten zien hoe u uw toepassing kunt configureren om aanmeldingen te accepteren van een Azure Active Directory-tenant (Azure AD). Als u uw toepassing wilt configureren voor *meerdere tenants*, kunt u een SaaS-toepassing (**Software as a Service**) aan een groot aantal organisaties aanbieden, zodat gebruikers zich kunnen aanmelden bij uw toepassing als zij toestemming hebben gegeven om hun account te gebruiken.

| Platform | Beschrijving | Koppeling |
| -------- | --------------------- | -------- |
| ![In deze afbeelding ziet u het JavaScript-logo](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA voor meerdere tenants roept Graph API aan |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![In deze afbeelding ziet u het Angular-logo](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA voor meerdere tenants roept aangepaste web-API voor meerdere tenants aan |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![In deze afbeelding ziet u het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC-webtoepassing roept Graph API aan |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![In deze afbeelding ziet u het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC-webtoepassing roept ASP.NET Core-web-API aan |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web-API's

De volgende voorbeelden laten zien hoe u een web-API kunt beveiligen met het Microsoft identity platform-eindpunt en hoe u een downstream API aanroept vanuit de web-API.

| Platform | Voorbeeld |
| -------- | ------------------- |
| ![In deze afbeelding ziet u het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core-web-API (service) van [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![In deze afbeelding ziet u het ASP.NET-logo](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web-API (service) van [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![In deze afbeelding ziet u het Java-logo](media/sample-v2-code/logo_java.png) | Web-API (service) van [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![In deze afbeelding ziet u het Node.js-logo](media/sample-v2-code/logo_nodejs.png) | Web-API (service) van [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![In deze afbeelding ziet u het Node.js-logo](media/sample-v2-code/logo_nodejs.png) | B2C-web-API (service) van [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions als web-API's

De volgende voorbeelden laten zien hoe u een Azure Function met HttpTrigger kunt beveiligen en een web-API kunt openstellen met het Microsoft identity platform-eindpunt en hoe u een downstream API aanroept vanuit de web-API.

| Platform | Voorbeeld |
| -------- | ------------------- |
| ![In deze afbeelding ziet u het ASP.NET Core-logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core-web-API (service) Azure Function van [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![In deze afbeelding ziet u het Node.js-logo](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web-API (service) van [NodeJS en passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![In deze afbeelding ziet u het Python-logo](media/sample-v2-code/logo_python.png)</p>Python | Web-API (service) van [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![In deze afbeelding ziet u het Node.js-logo](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web API (service) van [NodeJS en passport-azure-ad met namens](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Overige Microsoft Graph-voorbeelden

Zie de [voorbeelden en zelfstudies van de Microsoft Graph-community](https://github.com/microsoftgraph/msgraph-community-samples) voor meer informatie over [-voorbeelden](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) en zelfstudies die verschillende gebruikspatronen voor de Microsoft Graph API demonstreren, met inbegrip van verificatie met Azure AD.

## <a name="see-also"></a>Zie ook

- [Ontwikkelaarshandleiding voor Azure Active Directory (v1.0)](../azuread-dev/v1-overview.md)
- [Conceptuele en naslaginformatie over de Microsoft Graph API](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)