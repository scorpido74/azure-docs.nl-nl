---
title: Web-app die web-Api's aanroept (Schaf een token voor de app aan)-micro soft Identity-platform
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (een token voor de app ophalen)
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860621"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web-app die web-Api's aanroept-een Token ophalen voor de app

Nu u het client toepassings object hebt gemaakt, gebruikt u dit om een token te verkrijgen om een web-API aan te roepen. In ASP.NET of ASP.NET Core wordt het aanroepen van een web-API uitgevoerd in de controller. Het gaat ongeveer als volgt:

- Er wordt een token voor de Web-API opgehaald met de token cache. Als u dit token wilt ophalen, `AcquireTokenSilent`neemt u contact op.
- De beveiligde API met het toegangs token aan te roepen.

## <a name="aspnet-core"></a>ASP.NET Core

De controller methoden worden beveiligd door een `[Authorize]` kenmerk dat ervoor zorgt dat gebruikers worden geauthenticeerd om de web-app te gebruiken. Dit is de code die Microsoft Graph aanroept.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Hier volgt een vereenvoudigde code van de actie van de HomeController, waarmee een token wordt opgehaald om de Microsoft Graph aan te roepen.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Zie de stap 2 ([2-1-Web-app-aanroepen Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) van de zelf studie [MS-Identity-aspnetcore-zelf studie](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) voor meer informatie over de code die is vereist voor dit scenario.

Er zijn veel extra complexer, zoals:

- Een token cache implementeren voor de web-app (de zelf studie bevat verschillende implementaties)
- Het account wordt uit de cache verwijderd wanneer de gebruiker zich afmeldt
- Meerdere Api's aanroepen, waaronder incrementele toestemming

## <a name="aspnet"></a>ASP.NET

Dingen zijn vergelijkbaar in ASP.NET:

- Een controller actie die wordt beveiligd door een kenmerk [autoriseren], extraheert de Tenant-id `ClaimsPrincipal` en gebruikers-id van het lid van de controller. (ASP.NET gebruikt `HttpContext.User`.)
- Vanaf daar bouwt het een MSAL.NET `IConfidentialClientApplication`.
- Ten slotte wordt de `AcquireTokenSilent` methode van de vertrouwelijke client toepassing aangeroepen.

De code is vergelijkbaar met de code die wordt weer gegeven voor ASP.NET Core.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API aanroepen](scenario-web-app-call-api-call-api.md)
