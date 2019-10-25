---
title: De token cache wissen met behulp van micro soft-verificatie bibliotheek voor .NET
titleSuffix: Microsoft identity platform
description: Meer informatie over het wissen van de token cache met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
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
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd48078ec87b085a71ee23006d18917881894bf
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802926"
---
# <a name="clear-the-token-cache-using-msalnet"></a>De token cache wissen met behulp van MSAL.NET

Wanneer u [een toegangs token aanschaft](msal-acquire-cache-tokens.md) met behulp van micro soft Authentication Library voor .net (MSAL.net), wordt het token in de cache opgeslagen. Wanneer de toepassing een token vereist, moet het eerst de `AcquireTokenSilent`-methode aanroepen om te controleren of een acceptabel token zich in de cache bevindt. 

Het wissen van de cache wordt bereikt door de accounts uit de cache te verwijderen. Hiermee wordt de sessie cookie die zich in de browser bevindt, echter niet verwijderd.  In het volgende voor beeld wordt een open bare client toepassing geïnstantieerd, worden de accounts voor de toepassing opgehaald en worden de accounts verwijderd.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Lees [een toegangs Token ophalen](msal-acquire-cache-tokens.md)voor meer informatie over het verkrijgen en caching van tokens.
