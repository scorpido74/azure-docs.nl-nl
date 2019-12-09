---
title: Bureau blad-app die web-Api's aanroept, verplaatsen naar productie-micro soft Identity-platform | Azure
description: Meer informatie over het verplaatsen van een bureau blad-app die web-Api's aanroept voor productie
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a5e57d0ef37a3a2e758e42f122c8e014c94958
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919980"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Bureau blad-app voor het aanroepen van web-Api's-verplaatsen naar productie

In dit artikel vindt u informatie over het verplaatsen van uw bureau blad-app die web-Api's aanroept voor productie.

## <a name="handling-errors-in-desktop-applications"></a>Fouten afhandelen in bureaublad toepassingen

In de verschillende stromen hebt u geleerd hoe u de fouten voor de Stille stromen (zoals weer gegeven in code fragmenten) kunt afhandelen. U ziet ook dat er gevallen zijn waarin interactie nodig is (incrementele toestemming en voorwaardelijke toegang).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>De gebruiker vooraf toestemming geven voor verschillende bronnen

> [!NOTE]
> Het verkrijgen van toestemming voor verschillende bronnen werkt voor micro soft Identity platform, maar niet voor Azure Active Directory (Azure AD) B2C. Azure AD B2C ondersteunt alleen beheerders toestemming en geen toestemming van de gebruiker.

Met het micro soft Identity platform (v 2.0)-eind punt is het niet mogelijk om een token voor meerdere resources tegelijk op te halen. Daarom kan de para meter `scopes` alleen scopes voor één resource bevatten. U kunt ervoor zorgen dat de gebruiker vooraf toestuurt naar verschillende bronnen met behulp van de para meter `extraScopesToConsent`.

Als u bijvoorbeeld twee resources hebt die elk twee bereiken hebben:

- `https://mytenant.onmicrosoft.com/customerapi`-met 2 bereiken `customer.read` en `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-met 2 bereiken `vendor.read` en `vendor.write`

Gebruik de `.WithAdditionalPromptToConsent` modificator met de para meter `extraScopesToConsent`.

Bijvoorbeeld:

### <a name="in-msalnet"></a>In MSAL.NET

```CSharp
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

Doel-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Met deze aanroep krijgt u een toegangs token voor de eerste web-API.

Wanneer u de tweede Web-API moet aanroepen, kunt u `AcquireTokenSilent`-API aanroepen:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Een persoonlijk micro soft-account moet elke keer dat de app wordt uitgevoerd, toestemming geven

Voor gebruikers van micro soft-persoonlijke accounts vraagt de vraag om toestemming op elke systeem eigen client (Desktop/mobiele app) om toestemming te verlenen is het beoogde gedrag. De systeem eigen client identiteit is inherent onveilig (in tegens telling tot de vertrouwelijke client toepassing die een geheim uitwisselt met het micro soft-identiteits platform om hun identiteit te bewijzen). Het micro soft Identity-platform heeft ervoor gekozen om deze inbeveiliging te beperken voor consumenten Services door de gebruiker om toestemming te vragen, telkens wanneer de toepassing wordt geautoriseerd.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
