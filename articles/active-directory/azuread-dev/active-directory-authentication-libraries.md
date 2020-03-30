---
title: Azure Active Directory-verificatiebibliotheken | Microsoft Documenten
description: Met de Azure AD-verificatiebibliotheek (ADAL) kunnen ontwikkelaars van clienttoepassingen eenvoudig gebruikers verifiëren naar cloud- of on-premises Active Directory (AD) en vervolgens toegangstokens verkrijgen voor het beveiligen van API-aanroepen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 32375c14d95dc9e100cbf496c8550f0a195a11dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154607"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Libraries (ADAL)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Met de ADAL-verificatiebibliotheek (Azure Active Directory) v1.0 kunnen toepassingsontwikkelaars gebruikers verifiëren voor cloud- of on-premises Active Directory (AD) en tokens verkrijgen voor het beveiligen van API-aanroepen. ADAL maakt verificatie eenvoudiger voor ontwikkelaars door middel van functies zoals:

- Configureerbare tokencache die toegangstokens opslaat en tokens vernieuwt
- Automatisch vernieuwen van een token wanneer een toegangstoken verloopt en er een vernieuwingstoken beschikbaar is
- Ondersteuning voor asynchrone methodeaanroepen

> [!NOTE]
> Op zoek naar de Azure AD v2.0-bibliotheken (MSAL)? Bekijk de [MSAL-bibliotheekgids](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Door Microsoft ondersteunde clientbibliotheken

| Platform | Bibliotheek | Download | Broncode | Voorbeeld | Naslaginformatie
| --- | --- | --- | --- | --- | --- |
| .NET-client, Windows Store, UWP, Xamarin iOS en Android |ADAL .NET v3 |[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Desktop-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Naslaginformatie](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| JavaScript |ADAL.js |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[App van één pagina](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Naslaginformatie](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs (JavaDocs)](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js-web-app](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Naslaginformatie](https://docs.microsoft.com/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java-web-app](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Naslaginformatie](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-web-app](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Naslaginformatie](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Door Microsoft ondersteunde serverbibliotheken

| Platform | Bibliotheek | Download | Broncode | Voorbeeld | Naslaginformatie
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN voor AzureAD|[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN voor OpenIDConnect |[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Web-app](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN voor WS-Federatie |[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[MVC-webapp](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensies voor identiteitsprotocol voor .NET 4.5 |[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT Handler voor .NET 4.5 |[NuGet (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD-paspoort |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web-API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scenario's

Hier volgen drie veelvoorkomende scenario's voor het gebruik van ADAL in een client die toegang heeft tot een externe bron:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Gebruikers van een native clienttoepassing verifiëren die op een apparaat wordt uitgevoerd

In dit scenario heeft een ontwikkelaar een mobiele client of desktoptoepassing die toegang moet krijgen tot een externe bron, zoals een web-API. De web-API staat geen anonieme oproepen toe en moet worden aangeroepen in de context van een geverifieerde gebruiker. De web-API is vooraf geconfigureerd om toegangstokens te vertrouwen die zijn uitgegeven door een specifieke Azure AD-tenant. Azure AD is vooraf geconfigureerd om toegangstokens voor die bron uit te geven. Als u de web-API van de client wilt aanroepen, gebruikt de ontwikkelaar ADAL om verificatie met Azure AD te vergemakkelijken. De veiligste manier om ADAL te gebruiken is om het te laten renderen van de gebruikersinterface voor het verzamelen van gebruikersreferenties (weergegeven als browservenster).

ADAL maakt het eenvoudig om de gebruiker te verifiëren, een access token te verkrijgen en token van Azure AD te vernieuwen en vervolgens de web-API aan te roepen met behulp van het toegangstoken.

Zie [Native Client WPF Application to Web API](https://github.com/azureadsamples/nativeclient-dotnet)voor een codevoorbeeld dat dit scenario met verificatie naar Azure AD weergeeft.

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Een vertrouwelijke clienttoepassing verifiëren die op een webserver wordt uitgevoerd

In dit scenario heeft een ontwikkelaar een toepassing die wordt uitgevoerd op een server die toegang moet krijgen tot een externe bron, zoals een web-API. De web-API staat geen anonieme oproepen toe, dus moet deze worden aangeroepen vanuit een geautoriseerde service. De web-API is vooraf geconfigureerd om toegangstokens te vertrouwen die zijn uitgegeven door een specifieke Azure AD-tenant. Azure AD is vooraf geconfigureerd om toegangstokens voor die bron uit te geven aan een service met clientreferenties (client-id en geheim). ADAL vergemakkelijkt verificatie van de service met Azure AD die een toegangstoken retourt dat kan worden gebruikt om de web-API aan te roepen. ADAL verwerkt ook het beheer van de levensduur van het toegangstoken door het te cachingen en zo nodig te vernieuwen. Zie [Daemon console Application to Web API voor](https://github.com/AzureADSamples/Daemon-DotNet)een codevoorbeeld dat dit scenario aantoont.

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Een vertrouwelijke clienttoepassing verifiëren die op een server wordt uitgevoerd, namens een gebruiker

In dit scenario heeft een ontwikkelaar een webtoepassing die wordt uitgevoerd op een server die toegang moet krijgen tot een externe bron, zoals een web-API. De web-API staat geen anonieme oproepen toe, dus moet deze worden aangeroepen vanuit een geautoriseerde service namens een geverifieerde gebruiker. De web-API is vooraf geconfigureerd om toegangstokens te vertrouwen die zijn uitgegeven door een specifieke Azure AD-tenant en Azure AD is vooraf geconfigureerd om toegangstokens voor die bron uit te geven aan een service met clientreferenties. Zodra de gebruiker is geverifieerd in de webtoepassing, kan de toepassing een autorisatiecode voor de gebruiker krijgen van Azure AD. De webtoepassing kan adal vervolgens gebruiken om een toegangstoken te verkrijgen en een token namens een gebruiker te vernieuwen met behulp van de autorisatiecode en clientreferenties die zijn gekoppeld aan de toepassing van Azure AD. Zodra de webtoepassing in het bezit is van het toegangstoken, kan deze de web-API aanroepen totdat het token verloopt. Wanneer het token verloopt, kan de webtoepassing ADAL gebruiken om een nieuw toegangstoken te krijgen met behulp van het vernieuwingstoken dat eerder is ontvangen. Zie [Native client to Web API naar Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)voor een codevoorbeeld dat dit scenario aantoont.

## <a name="see-also"></a>Zie ook

- [De handleiding van de Azure Active Directory-ontwikkelaar](v1-overview.md)
- [Verificatiescenario's voor Azure Active directory](v1-authentication-scenarios.md)
- [Voorbeelden van Azure Active Directory-code](sample-v1-code.md)
