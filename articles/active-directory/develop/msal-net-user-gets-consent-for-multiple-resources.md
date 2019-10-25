---
title: Toestemming vragen voor verschillende bronnen (micro soft-verificatie bibliotheek voor .NET)
titleSuffix: Microsoft identity platform
description: Meer informatie over hoe een gebruiker vooraf toestemming kan krijgen voor verschillende bronnen met behulp van de micro soft Authentication Library voor .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a3d50e94f76341dc349eda997d6e25ca96dec0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802702"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Gebruiker krijgt toestemming voor verschillende resources met behulp van MSAL.NET
Met het micro soft Identity platform-eind punt kunt u geen token voor meerdere resources tegelijk ophalen. Wanneer u de micro soft Authentication Library voor .NET (MSAL.NET) gebruikt, mag de para meter scopes in de methode Acquire token alleen scopes voor één resource bevatten. U kunt echter vooraf toestemming geven voor verschillende bronnen door aanvullende bereiken te specificeren met behulp van de `.WithExtraScopeToConsent` Builder-methode.

> [!NOTE]
> Het verkrijgen van toestemming voor verschillende bronnen werkt voor micro soft Identity platform, maar niet voor Azure AD B2C. Azure AD B2C ondersteunt alleen beheerders toestemming en geen toestemming van de gebruiker.

Als u bijvoorbeeld twee resources hebt die elk 2 bereiken hebben:

- https:\/-mytenant.onmicrosoft.com/customerapi (met twee bereiken `customer.read` en `customer.write`)
- https:\/-mytenant.onmicrosoft.com/vendorapi (met twee bereiken `vendor.read` en `vendor.write`)

Gebruik de `.WithExtraScopeToConsent` modificator met de para meter *extraScopesToConsent* , zoals weer gegeven in het volgende voor beeld:

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
