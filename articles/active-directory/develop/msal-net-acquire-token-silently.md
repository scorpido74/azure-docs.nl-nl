---
title: Een token op de achtergrond verkrijgen (micro soft-verificatie bibliotheek voor .NET)
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe u een toegangs token op de achtergrond kunt verkrijgen (uit de token cache) met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802936"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Een Token ophalen uit de token cache met behulp van MSAL.NET

Wanneer u een toegangs token aanschaft met behulp van micro soft Authentication Library voor .NET (MSAL.NET), wordt het token in de cache opgeslagen. Wanneer de toepassing een token vereist, moet het eerst de `AcquireTokenSilent`-methode aanroepen om te controleren of een acceptabel token zich in de cache bevindt. In veel gevallen is het mogelijk om een andere token te verkrijgen met meer scopes op basis van een token in de cache. Het is ook mogelijk om een token te vernieuwen wanneer het bijna is verlopen (omdat de token cache ook een vernieuwings token bevat).

Het aanbevolen patroon is om eerst de `AcquireTokenSilent` methode aan te roepen.  Als `AcquireTokenSilent` mislukt, verschaf dan een token met behulp van andere methoden.

In het volgende voor beeld probeert de toepassing eerst een token op te halen uit de token cache.  Als er een `MsalUiRequiredException` uitzonde ring wordt gegenereerd, verkrijgt de toepassing interactief een token. 

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
