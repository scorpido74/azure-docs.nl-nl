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
ms.openlocfilehash: a77bb59afa753fa9d1655e787d4f7a18715ed2ca
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701583"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>Accounts verwijderen uit de cache op globale afmelding

U weet al hoe u aanmelden kunt toevoegen aan uw web-app. U leert dat in de [Web-app die zich aanmeldt gebruikers de aanmelding toevoegen](scenario-web-app-sign-user-sign-in.md).

Wat hier anders is, is dat wanneer de gebruiker zich afmeldt, vanuit deze toepassing of vanuit een wille keurige toepassing, u wilt verwijderen uit de token cache, de tokens die aan de gebruiker zijn gekoppeld.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Terugbellen onderscheppen na afmelden-eenmalige afmelding

Uw toepassing kan de gebeurtenis na het `logout` onderscheppen, bijvoorbeeld om de vermelding van de token cache te wissen die is gekoppeld aan het account dat is afgemeld. De web-app slaat toegangs tokens voor de gebruiker op in een cache. Wanneer u de na `logout`-call back onderschept, kan uw webtoepassing de gebruiker uit de token cache verwijderen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dit mechanisme wordt geïllustreerd in de `AddMsal()` methode [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

Met de **Afmeldings-URL** die u hebt geregistreerd voor uw toepassing, kunt u eenmalige afmelding implementeren. Het micro soft Identity platform `logout`-eind punt roept de **Afmeldings-URL** aan die is geregistreerd bij uw toepassing. Deze aanroep treedt op als de afmelding is geïnitieerd vanuit uw web-app of vanuit een andere web-app of via de browser. Zie [eenmalige afmelding](v2-protocols-oidc.md#single-sign-out)voor meer informatie.

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

De code voor RemoveAccountAsync is beschikbaar via [micro soft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Het ASP.NET-voor beeld verwijdert geen accounts uit de cache op de globale afmelding

# <a name="javatabjava"></a>[Java](#tab/java)

In het Java-voor beeld worden geen accounts uit de cache verwijderd voor de globale afmelding

# <a name="pythontabpython"></a>[Python](#tab/python)

Het python-voor beeld verwijdert geen accounts uit de cache op de globale afmelding

---

## <a name="next-steps"></a>Volgende stappen

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Een token voor de web-app ophalen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Een token voor de web-app ophalen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Een token voor de web-app ophalen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Een token voor de web-app ophalen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
