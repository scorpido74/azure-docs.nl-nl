---
title: Een web-app die zich aanmeldt bij gebruikers verplaatsen naar productie-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-app die wordt aangemeld bij gebruikers (verplaatsen naar productie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a8e275a41637950139598ac7c4f2513841bb4d0d
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112504"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Web-app die zich aanmeldt bij gebruikers: verplaatsen naar productie

Nu u weet hoe u een token kunt ophalen om Web-Api's aan te roepen, lees dan hoe u het kunt verplaatsen naar productie.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

### <a name="troubleshooting"></a>Problemen oplossen

> [!NOTE]
> Wanneer gebruikers zich voor de eerste keer aanmelden bij de webtoepassing, moeten ze toestemming geven. In sommige organisaties kunnen gebruikers echter een bericht zien als het volgende:
>
> *AppName heeft machtigingen nodig voor toegang tot resources in uw organisatie die alleen door een beheerder kunnen worden verleend. Vraag een beheerder om toestemming te verlenen voor deze app voordat u deze kunt gebruiken.*
>
> Dit komt doordat de Tenant beheerder de mogelijkheid voor gebruikers om toestemming heeft **uitgeschakeld** . In dat geval moet u contact opnemen met de Tenant beheerders zodat ze een beheerders toestemming voor de door de toepassing vereiste bereiken hebben.

### <a name="same-site"></a>Dezelfde site

Zorg ervoor dat u bekend bent met mogelijke problemen met nieuwe versies van de Chrome-browser

> [!div class="nextstepaction"]
> [Wijzigingen in SameSite-cookies afhandelen in de Chrome-browser](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scenario voor het aanroepen van web-Api's

Nadat uw web-app zich heeft aangemeld bij gebruikers, kan deze web-Api's namens de aangemelde gebruikers aanroepen. Het aanroepen van web-Api's vanuit de web-app is het object van het volgende scenario:

> [!div class="nextstepaction"]
> [Web-app die web-API's aanroept](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Grondige kennis: zelf studie voor ASP.NET Core web-app

Meer informatie over andere manieren om gebruikers aan te melden bij deze ASP.NET Core-zelf studie: 

> [!div class="nextstepaction"]
> [Uw web-apps inschakelen voor het aanmelden van gebruikers en het aanroepen van Api's met het micro soft-identiteits platform voor ontwikkel aars](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Deze progressieve zelf studie heeft productie-gereed code voor een web-app, waaronder het toevoegen van een aanmelding met accounts in:

- Uw organisatie
- Meerdere organisaties
- Werk-of school accounts of persoonlijke micro soft-accounts
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nationale Clouds

## <a name="sample-code-java-web-app"></a>Voorbeeld code: Java-Web-app

Meer informatie over de Java-Web-app in dit voor beeld op GitHub: 

> [!div class="nextstepaction"]
> [Een Java-webtoepassing die zich aanmeldt bij gebruikers met het micro soft-identiteits platform en aanroepen Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)
