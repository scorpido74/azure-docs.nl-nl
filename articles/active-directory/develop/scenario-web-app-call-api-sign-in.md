---
title: Web-app die web-Api's aanroept (Meld u aan)-micro soft Identity-platform
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (Meld u aan)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3036f8cb72f2a07673743a77e8be37614002563f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720198"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Web-app die web-Api's aanroept-aanmelden

U weet al hoe u aanmelden kunt toevoegen aan uw web-app. U leert dat in de [Web-app die zich aanmeldt gebruikers de aanmelding toevoegen](scenario-web-app-sign-user-sign-in.md).

Wat hier anders is, is dat wanneer de gebruiker zich afmeldt, vanuit deze toepassing of vanuit een wille keurige toepassing, u wilt verwijderen uit de token cache, de tokens die aan de gebruiker zijn gekoppeld.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Terugbellen onderscheppen na afmelden-eenmalige afmelding

Uw toepassing kan de gebeurtenis after `logout` onderscheppen, bijvoorbeeld om de vermelding van de token cache te wissen die is gekoppeld aan het account dat is afgemeld. De web-app slaat toegangs tokens voor de gebruiker op in een cache. Als u de retour waarde na `logout` onderschept, kan uw webtoepassing de gebruiker uit de token cache verwijderen.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dit mechanisme wordt geïllustreerd in de `AddMsal()`-methode van [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

Met de **Afmeldings-URL** die u hebt geregistreerd voor uw toepassing, kunt u eenmalige afmelding implementeren. Het micro soft Identity platform `logout`-eind punt roept de **Afmeldings-URL** aan die is geregistreerd bij uw toepassing. Deze aanroep treedt op als de afmelding is geïnitieerd vanuit uw web-app of vanuit een andere web-app of via de browser. Zie [eenmalige afmelding](v2-protocols-oidc.md#single-sign-out)voor meer informatie.

```CSharp
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

> [!div class="nextstepaction"]
> [Een token voor de web-app ophalen](scenario-web-app-call-api-acquire-token.md)
