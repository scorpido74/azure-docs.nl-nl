---
title: Web API die web API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een web-API die web-API's aanroept.
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
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885086"
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
