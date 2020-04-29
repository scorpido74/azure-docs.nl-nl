---
title: Code voorbeelden voor Azure Active Directory v 1.0 | Microsoft Docs
description: Biedt een index van code voorbeelden van Azure Active Directory (v 1.0-eind punt), geordend op scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ae283529abb3b71ee50fc710dd1ebe0d17a12be0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154828"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory code voorbeelden (v 1.0-eind punt)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

U kunt Microsoft Azure Active Directory (Azure AD) gebruiken om verificatie en autorisatie toe te voegen aan uw webtoepassingen en Web-Api's.

In deze sectie vindt u koppelingen naar voor beelden die u kunt gebruiken voor meer informatie over het Azure AD v 1.0-eind punt. In deze voor beelden ziet u hoe deze worden uitgevoerd, samen met code fragmenten die u in uw toepassingen kunt gebruiken. Op de pagina code voorbeeld vindt u gedetailleerde onderwerpen over lees mijzelf die u helpen bij vereisten, installatie en configuratie. En de code is een opmerking waarmee u de essentiële secties kunt begrijpen.

> [!NOTE]
> Als u geïnteresseerd bent in azure AD v2-code voorbeelden, raadpleegt u [v 2.0-code voorbeelden per scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Zie [verificatie scenario's voor Azure AD](v1-authentication-scenarios.md)voor meer informatie over het basis scenario voor elk type sample.

U kunt ook bijdragen aan onze voor beelden op GitHub. Zie Microsoft Azure Active Directory-voor [beelden en-documentatie](https://github.com/Azure-Samples?page=3&query=active-directory)voor meer informatie.

## <a name="single-page-applications"></a>Toepassingen met één pagina

In dit voor beeld ziet u hoe u een toepassing met één pagina schrijft die is beveiligd met Azure AD.

 Platform | Roept een eigen API aan | Hiermee wordt een andere web-API aangeroepen
 -------- |  --------------------- | ------------------ 
![Deze afbeelding toont het Java script-logo](media/sample-v2-code/logo-js.png) | [Java script-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Deze afbeelding toont het hoek JS-logo](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webtoepassingen

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webtoepassingen voor het aanmelden van gebruikers, het aanroepen van Microsoft Graph of een web-API met de identiteit van de gebruiker

De volgende voor beelden illustreren webtoepassingen die gebruikers ondertekenen. Sommige van deze toepassingen roepen ook de Microsoft Graph of uw eigen web-API aan, in de naam van de aangemelde gebruiker.

 Platform | Alleen tekenen in gebruikers | Aanroepen Microsoft Graph | Hiermee wordt een andere ASP.NET-of ASP.NET Core 2,0-Web-API aangeroepen
 -------- | ------------------- | --------------------- | -------------------------
![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2,0 | [DotNet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multi tenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD-grafiek) | [DotNet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [DotNet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [DotNet-webapp-multi tenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD-grafiek) |
![Deze afbeelding toont het python-logo](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![In deze afbeelding wordt het Java-logboek weer gegeven](media/sample-v2-code/logo-java.png)  | | [Java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Deze afbeelding toont het PHP-logo](media/sample-v2-code/logo-php.png) | | [PHP-graphapi-Web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webtoepassingen die op rollen gebaseerd toegangs beheer (autorisatie) demonstreren

De volgende voor beelden laten zien hoe u op rollen gebaseerd toegangs beheer (RBAC) implementeert. RBAC wordt gebruikt om de machtigingen van bepaalde functies in een webtoepassing te beperken tot bepaalde gebruikers. De gebruikers zijn gemachtigd, afhankelijk van of ze deel uitmaken van een **Azure AD-groep** of een **bepaalde toepassingsrol hebben.**

Platform | Voorbeeld |
 -------- | ------------------- |
![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [DotNet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [DotNet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Een .NET 4,5 MVC-Web-app die gebruikmaakt van Azure AD- **rollen** voor autorisatie

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktop-en Mobile Public client-toepassingen die Microsoft Graph of een web-API aanroepen

De volgende voor beelden illustreren open bare client toepassingen (deskto/pmobile-toepassingen) die toegang hebben tot de Microsoft Graph of een web-API in de naam van een gebruiker. Afhankelijk van de apparaten en platformen kunnen toepassingen zich op verschillende manieren aanmelden (stromen/subsidies):

- Interactief
- Op de achtergrond (met geïntegreerde Windows-verificatie in Windows of gebruikers naam/wacht woord)
- Door de interactieve aanmelding te delegeren naar een ander apparaat (apparaatcode stroom op apparaten die geen Webbe sturings elementen bieden)

Client toepassing | Platform | Stroom/verlenen | Aanroepen Microsoft Graph | Hiermee wordt een ASP.NET-of ASP.NET Core 2. x-Web-API aangeroepen
------------------ | -------- | ---------- | -------------------- | -------------------------
Bureau blad (WPF)           | ![In deze afbeelding wordt het .NET/C#-logo weer gegeven](media/sample-v2-code/logo-net.png)  | Interactief | Onderdeel van [DotNet-systeem eigen doel](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet-systeem eigen-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi-hand matige-JWT-validatie](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobiel (UWP)            | .![In deze afbeelding wordt de .NET/C#-/UWP weer gegeven](media/sample-v2-code/logo-windows.png)   | Interactief | [DotNet-systeem eigen-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Dit voor beeld maakt gebruik van [WAM](/windows/uwp/security/web-account-manager), niet [ADAL.net](https://aka.ms/adalnet)|  [DotNet-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-toepassing met behulp van ADAL.net om één Tenant Web-API aan te roepen) </p> [DotNet-webapi-multi tenant-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-toepassing met ADAL.net voor het aanroepen van een web-API met meerdere tenants)|
Mobiel (Android, iOS, UWP)   | ![In deze afbeelding wordt .NET/C# (Xamarin) weer gegeven](media/sample-v2-code/logo-xamarin.png) | Interactief | [DotNet-systeem eigen doel](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobiel (Android)           | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo-android.png) | Interactief |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobiel (iOS)           | ![Deze afbeelding toont iOS/objectief C of SWIFT](media/sample-v2-code/logo-ios.png) | Interactief |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Bureau blad (console)          | ![In deze afbeelding wordt het .NET/C#-logo weer gegeven](media/sample-v2-code/logo-net.png) | Gebruikers naam/wacht woord </p>  Geïntegreerde Windows-authenticatie | | [DotNet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Bureau blad (console)          | ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo-java.png) | Gebruikers naam/wacht woord | | [Java-Native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Bureau blad (console)           | ![In deze afbeelding wordt het .NET core/C#-logo weer gegeven](media/sample-v2-code/logo-netcore.png) | Toestel code stroom | | [DotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-toepassingen (toegang tot Web-Api's met de identiteit van de toepassing)

De volgende voor beelden tonen bureau blad-of webtoepassingen die toegang hebben tot de Microsoft Graph of een web-API zonder gebruiker (met de toepassings-id).

Client toepassing | Platform | Stroom/verlenen | Hiermee wordt een ASP.NET-of ASP.NET Core 2,0-Web-API aangeroepen
------------------ | -------- | ---------- | -------------------- 
Daemon-app (console)          | ![In deze afbeelding wordt het .NET-logo weer gegeven](media/sample-v2-code/logo-netframework.png) | Client referenties met geheim of certificaat van app | [DotNet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-daemon-certificaat-referentie](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon-app (console)         | ![In deze afbeelding wordt het .NET-logo weer gegeven](media/sample-v2-code/logo-netcore.png) | Client referenties met certificaat| [dotnetcore-daemon-certificaat-referentie](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-Web-app  | ![In deze afbeelding wordt het .NET-logo weer gegeven](media/sample-v2-code/logo-netframework.png) | Clientreferenties | [DotNet-webapp-webapi-oauth2-APPIDENTITY](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web-API's

### <a name="web-api-protected-by-azure-active-directory"></a>Web-API beveiligd door Azure Active Directory

In het volgende voor beeld ziet u hoe u een node. js-Web-API beveiligt met Azure AD.

In de vorige secties van dit artikel vindt u ook andere voor beelden van een client toepassing die een ASP.NET of ASP.NET Core web- **API** **aanroept** . Deze voor beelden worden niet opnieuw vermeld in deze sectie, maar u vindt ze in de laatste kolom van de bovenstaande tabellen of onder

| Platform | Voorbeeld |
|--------|-------------------|
| ![Deze afbeelding toont het node. js-logo](media/sample-v2-code/logo-nodejs.png)  | [knoop punt-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Web-API die Microsoft Graph of een andere web-API aanroept

In de volgende voor beelden ziet u een web-API die een andere web-API aanroept. Het tweede voor beeld laat zien hoe u voorwaardelijke toegang kunt verwerken.

| Platform |  Aanroepen Microsoft Graph | Hiermee wordt een andere ASP.NET-of ASP.NET Core 2,0-Web-API aangeroepen |
| -------- |  --------------------- | ------------------------- |
| ![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Andere Microsoft Graph-voor beelden

Zie [Microsoft Graph Community-voor beelden & zelf studies](https://github.com/microsoftgraph/msgraph-community-samples)voor voor beelden en zelf studies die verschillende gebruiks patronen demonstreren voor de Microsoft Graph-API, inclusief verificatie met Azure AD.

## <a name="see-also"></a>Zie ook

- [Ontwikkelaars handleiding Azure Active Directory](v1-overview.md)
- [Verificatie bibliotheken Azure Active Directory](active-directory-authentication-libraries.md)
- [Conceptuele en naslag informatie over Microsoft Graph-API](https://docs.microsoft.com/graph/use-the-api)
