---
title: Accounts verwijderen uit de tokencache bij afmelding - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het verwijderen van een account uit de tokencache bij afmelding
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881609"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Een web-app die web-API's aanroept: accounts verwijderen uit de tokencache bij wereldwijde afmelding

U hebt geleerd hoe u aanmelding toevoegt aan uw web-app in [de web-app die gebruikers aanmeldt: Aanmelden en afmelden.](scenario-web-app-sign-user-sign-in.md)

Afmelden is anders voor een web-app die webapis aanroept. Wanneer de gebruiker zich afmeldt bij uw toepassing of uit een toepassing, moet u de tokens die aan die gebruiker zijn gekoppeld, uit de tokencache verwijderen.

## <a name="intercept-the-callback-after-single-sign-out"></a>De terugroep na één afmelding onderscheppen

Als u de vermelding in de tokencache wilt wissen die `logout` is gekoppeld aan het account dat is afgemeld, kan uw toepassing de na-gebeurtenis onderscheppen. Webapps slaan toegangstokens op voor elke gebruiker in een tokencache. Door de na-terugroep `logout` te onderscheppen, kan uw webtoepassing de gebruiker uit de cache verwijderen.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Voor ASP.NET Core wordt het afluistermechanisme geïllustreerd in de `AddMsal()` methode van [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

Met de aanmeldings-URL die u eerder voor uw toepassing hebt geregistreerd, u één afmelding implementeren. Het eindpunt `logout` van het Microsoft-identiteitsplatform roept uw aanmeldings-URL aan. Dit gesprek vindt plaats als de afmelding is gestart vanuit uw web-app, of vanuit een andere web-app of de browser. Zie [Eenmalig aanmelden](v2-protocols-oidc.md#single-sign-out)voor meer informatie .

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

De code `RemoveAccountAsync` voor is beschikbaar op [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Het voorbeeld van ASP.NET verwijdert geen accounts uit de cache bij wereldwijde afmelding.

# <a name="java"></a>[Java](#tab/java)

Het Java-voorbeeld verwijdert geen accounts uit de cache bij wereldwijde afmelding.

# <a name="python"></a>[Python](#tab/python)

Het Python-voorbeeld verwijdert geen accounts uit de cache bij wereldwijde afmelding.

---

## <a name="next-steps"></a>Volgende stappen

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Een token voor de web-app aanschaffen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Een token voor de web-app aanschaffen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Een token voor de web-app aanschaffen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Een token voor de web-app aanschaffen](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
