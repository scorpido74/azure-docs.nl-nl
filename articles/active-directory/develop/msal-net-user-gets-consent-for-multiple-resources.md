---
title: Toestemming vragen voor verschillende resources (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe een gebruiker vooraf toestemming kan krijgen voor verschillende bronnen met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e36237e67b4498ca6aad4b7ffa8c645abeff6143
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477291"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Gebruiker krijgt toestemming voor verschillende resources met behulp van MSAL.NET
Met het micro soft Identity platform-eind punt kunt u geen token voor meerdere resources tegelijk ophalen. Wanneer u de micro soft Authentication Library voor .NET (MSAL.NET) gebruikt, mag de para meter scopes in de methode Acquire token alleen scopes voor één resource bevatten. U kunt echter vooraf toestemming geven voor verschillende bronnen door extra bereiken op te stellen met behulp van de `.WithExtraScopeToConsent` Builder-methode.

> [!NOTE]
> Het verkrijgen van toestemming voor verschillende bronnen werkt voor micro soft Identity platform, maar niet voor Azure AD B2C. Azure AD B2C ondersteunt alleen beheerders toestemming en geen toestemming van de gebruiker.

Als u bijvoorbeeld twee resources hebt die elk 2 bereiken hebben:

- https: \/ /mytenant.onmicrosoft.com/customerapi (met 2 bereiken `customer.read` en `customer.write` )
- https: \/ /mytenant.onmicrosoft.com/vendorapi (met 2 bereiken `vendor.read` en `vendor.write` )

U moet de `.WithExtraScopeToConsent` modificator met de para meter *extraScopesToConsent* gebruiken, zoals wordt weer gegeven in het volgende voor beeld:

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

Hiermee krijgt u een toegangs token voor de eerste web-API. Wanneer u vervolgens toegang tot de tweede Web-API nodig hebt, kunt u het token op de achtergrond verkrijgen via de token cache:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
