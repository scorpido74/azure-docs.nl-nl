---
title: Web-API voor het aanroepen van web-Api's-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-API voor het aanroepen van web-Api's.
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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701753"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Een web-API die web-Api's aanroept: een API aanroepen

Nadat u een token hebt, kunt u een beveiligde web-API aanroepen. U kunt dit doen via de controller van uw ASP.NET of ASP.NET Core Web-API.

## <a name="controller-code"></a>Controller code

Met de volgende code wordt de voorbeeld code die wordt weer gegeven in [een web-API die web-api's aanroept, voortgezet: Schaf een token voor de app aan](scenario-web-api-call-api-acquire-token.md). De code wordt aangeroepen in de acties van de API-controllers. Er wordt een stroomafwaartse API met de naam *ToDoList*aangeroepen.

Nadat u het token hebt aangeschaft, kunt u dit als Bearer-token gebruiken om de downstream API aan te roepen.

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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: verplaatsen naar productie](scenario-web-api-call-api-production.md)
