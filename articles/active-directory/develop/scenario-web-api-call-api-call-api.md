---
title: Web API die web API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een web-API die web-API's aanroept.
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76833376"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Een web-API die web-API's aanroept: een API aanroepen

Nadat u een token hebt, u een beveiligde web-API aanroepen. Dit doe je vanuit de controller van je web-API.

## <a name="controller-code"></a>Controllercode

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

De volgende code blijft de voorbeeldcode die wordt weergegeven in [een web-API die web-API's aanroept: Een token voor de app aanschaffen.](scenario-web-api-call-api-acquire-token.md) De code wordt aangeroepen in de acties van de API-controllers. Het roept een downstream API genaamd *todolist*.

Nadat u het token hebt aangeschaft, gebruikt u het als een token voor dragers om de downstream-API aan te roepen.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

De volgende code blijft de voorbeeldcode die wordt weergegeven in [een web-API die web-API's aanroept: Een token voor de app aanschaffen.](scenario-web-api-call-api-acquire-token.md) De code wordt aangeroepen in de acties van de API-controllers. Het noemt de downstream API MS Graph.

Nadat u het token hebt aangeschaft, gebruikt u het als een token voor dragers om de downstream-API aan te roepen.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Een voorbeeld dat deze stroom met MSAL Python aantoont, is nog niet beschikbaar.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-API's aanroept: naar productie gaan](scenario-web-api-call-api-production.md)
