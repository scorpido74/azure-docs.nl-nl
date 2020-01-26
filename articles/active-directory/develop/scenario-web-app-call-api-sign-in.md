---
title: Accounts uit de token cache verwijderen bij het afmelden-micro soft Identity-platform | Azure
description: Meer informatie over het verwijderen van een account uit de token cache bij afmelden
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758867"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Een web-app die web-Api's aanroept: verwijdert accounts uit de token cache op Global Sign-out

U hebt geleerd hoe u aanmelden kunt toevoegen aan uw web-app in een web-app die zich aanmeldt [bij gebruikers: aanmelden en afmelden](scenario-web-app-sign-user-sign-in.md).

Afmelden is anders voor een web-app die web-api's aanroept. Wanneer de gebruiker zich afmeldt bij uw toepassing of vanuit een wille keurige toepassing, moet u de tokens die zijn gekoppeld aan die gebruiker uit de token cache verwijderen.

## <a name="intercept-the-callback-after-single-sign-out"></a>Terugbellen onderscheppen na eenmalige afmelding

Als u de token-cache vermelding wilt wissen die is gekoppeld aan het account dat is afgemeld, kan uw toepassing de gebeurtenis na het `logout` onderscheppen. Web-apps slaan toegangs tokens voor elke gebruiker op in een token cache. Door de `logout`-call back te onderscheppen, kan uw webtoepassing de gebruiker uit de cache verwijderen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Voor ASP.NET Core wordt het onderschepings mechanisme geïllustreerd in de methode `AddMsal()` van [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

Met de afmeldings-URL die u eerder hebt geregistreerd voor uw toepassing, kunt u eenmalige afmelding implementeren. Het micro soft Identity platform `logout`-eind punt belt uw afmeldings-URL. Deze aanroep treedt op als de registratie is gestart vanuit uw web-app of vanuit een andere web-app of via de browser. Zie [eenmalige afmelding](v2-protocols-oidc.md#single-sign-out)voor meer informatie.

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

De code voor `RemoveAccountAsync` is beschikbaar via [micro soft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Het ASP.NET-voor beeld verwijdert geen accounts uit de cache op globale afmelding.

# <a name="javatabjava"></a>[Java](#tab/java)

In het Java-voor beeld worden geen accounts uit de cache verwijderd voor de globale afmelding.

# <a name="pythontabpython"></a>[Python](#tab/python)

Het python-voor beeld verwijdert geen accounts uit de cache op globale afmelding.

---

## <a name="next-steps"></a>Volgende stappen

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Een token verkrijgen voor de web-app](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
