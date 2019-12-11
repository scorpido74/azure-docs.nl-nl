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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6063d143e2f217426bdf1db217fde46f8542d314
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965208"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Web-API voor het aanroepen van web-Api's-aanroepen van een API

Zodra u een token hebt, kunt u een beveiligde web-API aanroepen. Dit wordt gedaan vanuit de controller van uw ASP.NET/ASP.NET core web-API.

## <a name="controller-code"></a>Controller code

Dit is de voortzetting van de voorbeeld code die wordt weer gegeven in de [beveiligde web-API aanroepen Web-api's: een Token ophalen](scenario-web-api-call-api-acquire-token.md), aangeroepen in de acties van de API-controllers, waarmee een downstream API (met de naam ToDoList) wordt opgeroepen.

Nadat u het token hebt verkregen, gebruikt u dit als Bearer-token om de downstream API aan te roepen.

```CSharp
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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-web-api-call-api-production.md)
