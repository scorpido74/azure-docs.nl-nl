---
title: Ontvang een token voor een web-API die web-API's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het maken van een web-API die web-API's aanroept waarvoor een token voor de app moet worden verkreeg.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885137"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Een web-API die web-API's aanroept: een token voor de app aanschaffen

Nadat u een clienttoepassingsobject hebt gebouwd, gebruikt u het om een token te verkrijgen waarmee u een web-API aanroepen.

## <a name="code-in-the-controller"></a>Code in de controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Hier is een voorbeeld van code die wordt aangeroepen in de acties van de API-controllers. Het roept een downstream API genaamd *todolist*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()`is vergelijkbaar met het scenario in [een web-API die web-API's aanroept: app-configuratie](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`meteen `IConfidentialClientApplication` met een cache die informatie bevat voor slechts één account. De rekening wordt `GetAccountIdentifier` verstrekt door de methode.

De `GetAccountIdentifier` methode maakt gebruik van de claims die zijn gekoppeld aan de identiteit van de gebruiker voor wie de web-API het JSON-webtoken (JWT) heeft ontvangen:

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
Hier is een voorbeeld van code die wordt aangeroepen in de acties van de API-controllers. Het noemt de downstream API - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

Een Python-web-API moet een aantal middleware gebruiken om het token aan toonder dat van de client is ontvangen, te valideren. De web-API kan vervolgens het toegangstoken voor downstream-API [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) verkrijgen met behulp van MSAL Python-bibliotheek door de methode aan te roepen. Een voorbeeld dat deze stroom met MSAL Python aantoont, is nog niet beschikbaar.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-API's aanroept: een API aanroepen](scenario-web-api-call-api-call-api.md)
