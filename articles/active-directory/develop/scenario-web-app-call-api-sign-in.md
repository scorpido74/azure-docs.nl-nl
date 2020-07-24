---
title: Accounts uit de token cache verwijderen bij het afmelden-micro soft Identity-platform | Azure
description: Meer informatie over het verwijderen van een account uit de token cache bij afmelden
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: be8208c7b2b67610f8d579b1bc0094bf09b3033e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026285"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Een web-app die web-Api's aanroept: verwijdert accounts uit de token cache op Global Sign-out

U hebt geleerd hoe u aanmelden kunt toevoegen aan uw web-app in een web-app die zich aanmeldt [bij gebruikers: aanmelden en afmelden](scenario-web-app-sign-user-sign-in.md).

Afmelden is anders voor een web-app die web-api's aanroept. Wanneer de gebruiker zich afmeldt bij uw toepassing of vanuit een wille keurige toepassing, moet u de tokens die zijn gekoppeld aan die gebruiker uit de token cache verwijderen.

## <a name="intercept-the-callback-after-single-sign-out"></a>Terugbellen onderscheppen na eenmalige afmelding

Als u de token-cache vermelding wilt wissen die is gekoppeld aan het account waarmee u zich hebt afgemeld, kan uw toepassing de gebeurtenis after onderscheppen `logout` . Web-apps slaan toegangs tokens voor elke gebruiker op in een token cache. Door de `logout` terugroeping na terugbellen te onderscheppen, kan uw webtoepassing de gebruiker uit de cache verwijderen.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Micro soft. Identity. Web zorgt voor het implementeren van afmeldingen voor u. Zie [micro soft. Identity. web source code](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176) voor meer informatie.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Het ASP.NET-voor beeld verwijdert geen accounts uit de cache op globale afmelding.

# <a name="java"></a>[Java](#tab/java)

In het Java-voor beeld worden geen accounts uit de cache verwijderd voor de globale afmelding.

# <a name="python"></a>[Python](#tab/python)

Het python-voor beeld verwijdert geen accounts uit de cache op globale afmelding.

---

## <a name="next-steps"></a>Volgende stappen

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
