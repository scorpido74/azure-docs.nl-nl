---
title: Codevoorbeelden voor Azure Active Directory v1.0 | Microsoft Documenten
description: Biedt een index van Azure Active Directory -codesamples (v1.0-eindpunten) die zijn geordend op scenario.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154828"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory-codevoorbeelden (v1.0-eindpunt)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

U Microsoft Azure Active Directory (Azure AD) gebruiken om verificatie en autorisatie toe te voegen aan uw webtoepassingen en web-API's.

In deze sectie vindt u koppelingen naar voorbeelden die u gebruiken voor meer informatie over het Azure AD v1.0-eindpunt. Deze voorbeelden laten u zien hoe het wordt gedaan, samen met codefragmenten die u gebruiken in uw toepassingen. Op de voorbeeldpagina van de code vindt u gedetailleerde lees-me-onderwerpen die helpen bij vereisten, installatie en installatie. En de code wordt becommentarieerd om u te helpen de kritieke secties te begrijpen.

> [!NOTE]
> Als u geïnteresseerd bent in Azure AD V2-codevoorbeelden, raadpleegt [u v2.0-codevoorbeelden per scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Zie [Verificatiescenario's voor Azure AD](v1-authentication-scenarios.md)voor elk voorbeeldtype als u het basisscenario voor elk voorbeeldtype wilt begrijpen.

U ook bijdragen aan onze samples op GitHub. Zie [Microsoft Azure Active Directory-voorbeelden en -documentatie](https://github.com/Azure-Samples?page=3&query=active-directory)voor meer informatie.

## <a name="single-page-applications"></a>Toepassingen met één pagina

In dit voorbeeld ziet u hoe u een toepassing van één pagina schrijven die is beveiligd met Azure AD.

 Platform | Roept zijn eigen API aan | Roept een andere web-API aan
 -------- |  --------------------- | ------------------ 
![Deze afbeelding toont het JavaScript-logo](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Deze afbeelding toont het Angular JS-logo](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webtoepassingen

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webtoepassingen die gebruikers aanmelden, Microsoft Graph aanroepen of een web-API met de identiteit van de gebruiker

De volgende voorbeelden illustreren webtoepassingen die gebruikers ondertekenen. Sommige van deze toepassingen noemen ook de Microsoft Graph of uw eigen Web API, op naam van de aangemelde gebruiker.

 Platform | Alleen aan-het eind van de gebruikers | Oproepen in Microsoft Graph | Roept een andere ASP.NET of ASP.NET Core 2.0 Web API
 -------- | ------------------- | --------------------- | -------------------------
![Deze afbeelding toont het ASP.NET logo](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD-grafiek) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Deze afbeelding toont het ASP.NET logo](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD-grafiek) |
![Deze afbeelding toont het Python-logo](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Deze afbeelding toont de Java-log](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Deze afbeelding toont het PHP-logo](media/sample-v2-code/logo-php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webtoepassingen die functiegebaseerde toegangscontrole aantonen (autorisatie)

In de volgende voorbeelden wordt uitgelegd hoe u op rollen gebaseerdtoegangscontrole (RBAC) implementeren. RBAC wordt gebruikt om de machtigingen van bepaalde functies in een webtoepassing te beperken tot bepaalde gebruikers. De gebruikers zijn geautoriseerd, afhankelijk van of ze behoren tot een **Azure AD-groep** of een bepaalde **toepassingsrol**hebben.

Platform | Voorbeeld |
 -------- | ------------------- |
![Deze afbeelding toont het ASP.NET logo](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Een .NET 4.5 MVC-webapp die Azure **AD-rollen** gebruikt voor autorisatie

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Desktop- en mobiele openbare clienttoepassingen die Microsoft Graph of een web-API aanroepen

De volgende voorbeelden illustreren openbare clienttoepassingen (deskto/pmobile-toepassingen) die toegang hebben tot de Microsoft Graph of een web-API in naam van een gebruiker. Afhankelijk van de apparaten en platforms kunnen toepassingen gebruikers op verschillende manieren aanmelden (stromen/subsidies):

- Interactief
- Geruisloos (met geïntegreerde Windows-verificatie op Windows, of gebruikersnaam/wachtwoord)
- Door de interactieve aanmelding te delegeren naar een ander apparaat (apparaatcodestroom wordt gebruikt op apparaten die geen webbesturingselementen bieden)

Clienttoepassing | Platform | Stroom/subsidie | Oproepen in Microsoft Graph | Roept een ASP.NET of ASP.NET Core 2.x Web API aan
------------------ | -------- | ---------- | -------------------- | -------------------------
Bureaublad (WPF)           | ![Deze afbeelding toont het .NET/C#-logo](media/sample-v2-code/logo-net.png)  | Interactief | Onderdeel van [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validatie](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobiel (UWP)            | .![Deze afbeelding toont de .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interactief | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Dit voorbeeld maakt gebruik van [WAM,](/windows/uwp/security/web-account-manager)niet [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store (UWP-toepassing](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) met ADAL.NET om één tenant web-API aan te roepen) </p> [dotnet-webapi-multitenant-windows-store (UWP-toepassing](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) met ADAL.NET om een multi-tenant web-API aan te roepen)|
Mobiel (Android, iOS, UWP)   | ![Deze afbeelding toont de .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interactief | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobiel (Android)           | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo-android.png) | Interactief |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobiel (iOS)           | ![Deze afbeelding toont iOS / Doelstelling C of Swift](media/sample-v2-code/logo-ios.png) | Interactief |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Bureaublad (console)          | ![Deze afbeelding toont het .NET/C#-logo](media/sample-v2-code/logo-net.png) | Gebruikersnaam / wachtwoord </p>  Geïntegreerde Windows-authenticatie | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Bureaublad (console)          | ![Deze afbeelding toont het Java-logo](media/sample-v2-code/logo-java.png) | Gebruikersnaam / wachtwoord | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Bureaublad (console)           | ![Deze afbeelding toont het .NET Core/C#-logo](media/sample-v2-code/logo-netcore.png) | Apparaatcodestroom | | [dotnet-deviceprofiel](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-toepassingen (toegang tot web-API's met de identiteit van de toepassing)

In de volgende voorbeelden worden bureaublad- of webtoepassingen weergegeven die toegang hebben tot de Microsoft Graph of een web-API zonder gebruiker (met de toepassingsidentiteit).

Clienttoepassing | Platform | Stroom/subsidie | Roept een ASP.NET of ASP.NET Core 2.0-web-API aan
------------------ | -------- | ---------- | -------------------- 
Daemon-app (console)          | ![Deze afbeelding toont het .NET-logo](media/sample-v2-code/logo-netframework.png) | Clientreferenties met app-geheim of certificaat | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificaat-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon-app (console)         | ![Deze afbeelding toont het .NET-logo](media/sample-v2-code/logo-netcore.png) | Clientreferenties met certificaat| [dotnetcore-daemon-certificaat-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web App  | ![Deze afbeelding toont het .NET-logo](media/sample-v2-code/logo-netframework.png) | Clientreferenties | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web-API's

### <a name="web-api-protected-by-azure-active-directory"></a>Web-API die is beveiligd door Azure Active Directory

In het volgende voorbeeld ziet u hoe u een web-API van node.js beveiligen met Azure AD.

In de vorige delen van dit artikel u ook andere voorbeelden vinden die een clienttoepassing illustreren die een ASP.NET of ASP.NET Core **Web API aanroept.** **calling** Deze voorbeelden worden niet opnieuw vermeld in deze sectie, maar u vindt ze in de laatste kolom van de tabellen boven of onder

| Platform | Voorbeeld |
|--------|-------------------|
| ![Deze afbeelding toont het Node.js-logo](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Web-API die Microsoft Graph aanroept of een andere web-API

In de volgende voorbeelden wordt een web-API gedemonstreerd die een andere web-API aanroept. In het tweede voorbeeld ziet u hoe u voorwaardelijke toegang verwerken.

| Platform |  Oproepen in Microsoft Graph | Roept een andere ASP.NET of ASP.NET Core 2.0 Web API |
| -------- |  --------------------- | ------------------------- |
| ![Deze afbeelding toont het ASP.NET logo](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Andere Voorbeelden van Microsoft Graph

Zie [Microsoft Graph Community Samples & Tutorials](https://github.com/microsoftgraph/msgraph-community-samples)voor voorbeelden en zelfstudies die verschillende gebruikspatronen voor de Microsoft Graph-API demonstreren, waaronder verificatie met Azure AD.

## <a name="see-also"></a>Zie ook

- [Handleiding voor Azure Active Directory-ontwikkelaars](v1-overview.md)
- [Azure Active Directory-verificatiebibliotheken](active-directory-authentication-libraries.md)
- [Conceptueel en naslagwerk van Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)
