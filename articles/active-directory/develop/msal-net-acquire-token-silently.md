---
title: Een token ophalen uit de cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Meer informatie over het stilletjes aanschaffen van een access token (vanuit de tokencache) met behulp van de Microsoft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084832"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Een token ophalen uit de tokencache met MSAL.NET

Wanneer u een toegangstoken aandoet met Microsoft Authentication Library voor .NET (MSAL.NET), wordt het token in de cache opgeslagen. Wanneer de toepassing een token nodig `AcquireTokenSilent` heeft, moet deze eerst de methode aanroepen om te controleren of er een acceptabel token in de cache is. In veel gevallen is het mogelijk om een ander token te kopen met meer scopes op basis van een token in de cache. Het is ook mogelijk om een token te vernieuwen wanneer het bijna verloopt (omdat de tokencache ook een vernieuwingstoken bevat).

Het aanbevolen patroon is `AcquireTokenSilent` om de methode eerst aan te roepen.  Als `AcquireTokenSilent` dit niet lukt, verwerf t u een token met behulp van andere methoden.

In het volgende voorbeeld probeert de toepassing eerst een token uit de tokencache te verkrijgen.  Als `MsalUiRequiredException` er een uitzondering wordt gemaakt, krijgt de toepassing interactief een token. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
