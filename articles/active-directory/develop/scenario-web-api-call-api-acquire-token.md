---
title: Een Token ophalen voor een web-API die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API voor het aanroepen van web-Api's die een token moeten verkrijgen voor de app.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e0701cc5a9bb14800a48e2281dba1eb6ea0cf72
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026455"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Een web-API die web-Api's aanroept: een Token ophalen voor de app

Nadat u een client toepassings object hebt gemaakt, gebruikt u dit om een token op te halen dat u kunt gebruiken om een web-API aan te roepen.

## <a name="code-in-the-controller"></a>Code in de controller

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Hier volgt een voor beeld van code met behulp van micro soft. Identity. Web dat wordt aangeroepen in de acties van de API-controllers. Er wordt een stroomafwaartse API met de naam *ToDoList*aangeroepen. Als u een token wilt ophalen voor het aanroepen van de stroomafwaartse API, kunt u de service injecteren `ITokenAcquisition` door de afhankelijkheids injectie in de constructor van uw controller (of uw pagina-constructor als u een razendsnelle functie gebruikt) en u deze gebruiken in de controller acties, waarbij een token wordt opgehaald voor de gebruiker ( `GetAccessTokenForUserAsync` ) of voor de toepassing zelf ( `GetAccessTokenForAppAsync` ) in het geval van een daemon-scenario

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Zie voor meer informatie over de `callTodoListService` -methode [een web-API die web-api's aanroept: roep een API](scenario-web-api-call-api-call-api.md)aan.

# <a name="java"></a>[Java](#tab/java)
Hier volgt een voor beeld van code die wordt aangeroepen in de acties van de API-controllers. Het roept de downstream API-Microsoft Graph aan.

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

Een python-Web-API moet een middleware gebruiken om het Bearer-token te valideren dat van de client is ontvangen. De Web-API kan vervolgens het toegangs token voor downstream API verkrijgen met behulp van de MSAL python-bibliotheek door de methode aan te roepen [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . Een voor beeld van het demonstreren van deze stroom met MSAL python is nog niet beschikbaar.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: een API aanroepen](scenario-web-api-call-api-call-api.md)
