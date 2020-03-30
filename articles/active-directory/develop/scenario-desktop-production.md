---
title: Web-API's voor bureaublad-apps verplaatsen naar productie - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het verplaatsen van een bureaublad-app die web-API's naar productie roept
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c8a9cf0c05d8af14d52bb1efb536dc8bbe7db84d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262566"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Bureaublad-app die web-API's aanroept: naar productie gaan

In dit artikel leert u hoe u uw bureaublad-app verplaatst die web-API's naar productie roept.

## <a name="handle-errors-in-desktop-applications"></a>Fouten in bureaubladtoepassingen verwerken

In de verschillende stromen hebt u geleerd hoe u de fouten voor de stille stromen moet verwerken, zoals weergegeven in de codefragmenten. Je hebt ook gezien dat er gevallen zijn waarin interactie nodig is, zoals in incrementele toestemming en voorwaardelijke toegang.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>De gebruiker vooraf toestemming geven voor verschillende bronnen

> [!NOTE]
> Het verkrijgen van toestemming voor verschillende bronnen werkt voor microsoft-identiteitsplatform, maar niet voor Azure Active Directory (Azure AD) B2C. Azure AD B2C ondersteunt alleen toestemming van beheerders, niet toestemming van de gebruiker.

U geen token voor meerdere bronnen tegelijk krijgen met het Microsoft-identiteitsplatform (v2.0) eindpunt. De `scopes` parameter kan scopes bevatten voor slechts één resource. U ervoor zorgen dat de gebruiker vooraf `extraScopesToConsent` toestemming geeft voor verschillende bronnen met behulp van de parameter.

U hebt bijvoorbeeld twee resources met elk twee scopes:

- `https://mytenant.onmicrosoft.com/customerapi`met de `customer.read` scopes en`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`met de `vendor.read` scopes en`vendor.write`

Gebruik in dit `.WithAdditionalPromptToConsent` voorbeeld de modifier die de `extraScopesToConsent` parameter heeft.

Bijvoorbeeld:

### <a name="in-msalnet"></a>In MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doelstelling-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Met deze oproep krijgt u een toegangstoken voor de eerste web-API.

Wanneer u de tweede web-API `AcquireTokenSilent` moet aanroepen, roept u de API aan.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>Microsoft persoonlijk account vereist hertoestemming elke keer dat de app wordt uitgevoerd

Voor gebruikers van persoonlijke gebruikers van microsoft-gebruikers is het verzoek om toestemming voor elke native client (desktop of mobiele app) om te autoriseren het beoogde gedrag. Native client identiteit is inherent onzeker, wat in strijd is met vertrouwelijke client applicatie identiteit. Vertrouwelijke clienttoepassingen wisselen een geheim uit met het Microsoft Identity-platform om hun identiteit te bewijzen. Het Microsoft-identiteitsplatform heeft ervoor gekozen deze onzekerheid voor consumentenservices te beperken door de gebruiker om toestemming te vragen telkens wanneer de toepassing is geautoriseerd.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
