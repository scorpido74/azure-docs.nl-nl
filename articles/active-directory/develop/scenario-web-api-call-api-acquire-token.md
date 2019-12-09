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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 276ff1e5e9f709aa5b38d1efa4055dfe3baf3cc5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919780"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web-API voor het aanroepen van web-Api's-een Token ophalen voor de app

Zodra u een client toepassings object hebt gemaakt, gebruikt u dit om een token op te halen dat u kunt gebruiken om een web-API aan te roepen.

## <a name="code-in-the-controller"></a>Code in de controller

Hier volgt een voor beeld van code die wordt aangeroepen in de acties van de API-controllers, aanroepen van een stroomafwaartse API (met de naam ToDoList).

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
}
```

`BuildConfidentialClient()` is vergelijkbaar met wat u hebt gezien in de [Web-API voor het artikel die web-api's aanroept-app-configuratie](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instantieert `IConfidentialClientApplication` met een cache die alleen informatie voor één account bevat. Het account wordt verzorgd door de `GetAccountIdentifier` methode.

De `GetAccountIdentifier` methode maakt gebruik van de claims die zijn gekoppeld aan de identiteit van de gebruiker waarvoor de Web-API de JWT heeft ontvangen:

```CSharp
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
> [Een web-API aanroepen](scenario-web-api-call-api-call-api.md)
