---
title: Een Token ophalen voor een web-API die web-Api's aanroept | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een web-API voor het aanroepen van web-Api's die een token moeten verkrijgen voor de app.
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
ms.openlocfilehash: 1c3d6f8d47152d70bdeaabbbc6d7b81187291857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701804"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Een web-API die web-Api's aanroept: een Token ophalen voor de app

Nadat u een client toepassings object hebt gemaakt, gebruikt u dit om een token op te halen dat u kunt gebruiken om een web-API aan te roepen.

## <a name="code-in-the-controller"></a>Code in de controller

Hier volgt een voor beeld van code die wordt aangeroepen in de acties van de API-controllers. Er wordt een stroomafwaartse API met de naam *ToDoList*aangeroepen.

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

`BuildConfidentialClient()` is vergelijkbaar met het scenario in [een web-API die web-api's aanroept: app-configuratie](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` maakt `IConfidentialClientApplication` een exemplaar van een cache dat informatie bevat voor slechts één account. Het account wordt verzorgd door de `GetAccountIdentifier` methode.

De `GetAccountIdentifier` methode maakt gebruik van de claims die zijn gekoppeld aan de identiteit van de gebruiker voor wie de Web-API de JSON Web Token (JWT) heeft ontvangen:

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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: een API aanroepen](scenario-web-api-call-api-call-api.md)
