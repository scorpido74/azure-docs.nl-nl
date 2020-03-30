---
title: Toestemming krijgen voor verschillende bronnen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe een gebruiker vooraf toestemming kan krijgen voor verschillende bronnen met behulp van de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085837"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Gebruiker krijgt toestemming voor verschillende bronnen met behulp van MSAL.NET
Met het eindpunt van het Microsoft-identiteitsplatform u geen token voor meerdere bronnen tegelijk krijgen. Wanneer u de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET) gebruikt, mogen de parameters voor scopes in de methode acquire token slechts scopes voor één resource bevatten. U echter vooraf vooraf toestemming geven voor verschillende resources `.WithExtraScopeToConsent` door extra scopes op te geven met behulp van de bouwermethode.

> [!NOTE]
> Het verkrijgen van toestemming voor verschillende bronnen werkt voor het Microsoft-identiteitsplatform, maar niet voor Azure AD B2C. Azure AD B2C ondersteunt alleen toestemming van beheerders, niet toestemming van de gebruiker.

Als u bijvoorbeeld twee resources hebt met elk twee scopes:

- https:\//mytenant.onmicrosoft.com/customerapi (met `customer.read` 2 `customer.write`scopes en )
- https:\//mytenant.onmicrosoft.com/vendorapi (met `vendor.read` 2 `vendor.write`scopes en )

U moet `.WithExtraScopeToConsent` de modifier gebruiken die de parameter *extraScopesToConsent* heeft, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Hiermee krijg je een toegangstoken voor de eerste web-API. Wanneer u vervolgens toegang nodig hebt tot de tweede web-API, u het token in stilte verkrijgen uit de tokencache:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
