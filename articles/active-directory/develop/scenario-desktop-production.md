---
title: Bureau blad-app voor het aanroepen van web-Api's (verplaatsen naar productie)-micro soft Identity-platform
description: Meer informatie over het bouwen van een desktop-app die web-Api's aanroept (verplaatsen naar productie)
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a353b4577f8cfa9ba279ad2793e1a7ab8b27e55
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268327"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Bureau blad-app voor het aanroepen van web-Api's-verplaatsen naar productie

In dit artikel vindt u meer informatie over het verbeteren van uw toepassing en het verplaatsen ervan naar productie.

## <a name="handling-errors-in-desktop-applications"></a>Fouten afhandelen in bureaublad toepassingen

In de verschillende stromen hebt u geleerd hoe u de fouten voor de Stille stromen (zoals weer gegeven in code fragmenten) kunt afhandelen. U ziet ook dat er gevallen zijn waarin interactie nodig is (incrementele toestemming en voorwaardelijke toegang).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>De gebruiker vooraf toestemming geven voor verschillende bronnen

> [!NOTE]
> Het verkrijgen van toestemming voor verschillende bronnen werkt voor micro soft Identity platform, maar niet voor Azure Active Directory (Azure AD) B2C. Azure AD B2C ondersteunt alleen beheerders toestemming en geen toestemming van de gebruiker.

Met het micro soft Identity platform (v 2.0)-eind punt is het niet mogelijk om een token voor meerdere resources tegelijk op te halen. Daarom kan de `scopes` para meter alleen scopes voor één resource bevatten. U kunt ervoor zorgen dat de gebruiker vooraf toestuurt naar verschillende bronnen met behulp `extraScopesToConsent` van de para meter.

Als u bijvoorbeeld twee resources hebt die elk twee bereiken hebben:

- `https://mytenant.onmicrosoft.com/customerapi`-met 2 bereiken `customer.read` en`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-met 2 bereiken `vendor.read` en`vendor.write`

U moet de `.WithAdditionalPromptToConsent` modificator met de `extraScopesToConsent` para meter gebruiken.

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

Wanneer u de tweede Web-API moet aanroepen, kunt u `AcquireTokenSilent` de volgende API aanroepen:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Een persoonlijk micro soft-account moet elke keer dat de app wordt uitgevoerd, toestemming geven

Voor gebruikers van micro soft-persoonlijke accounts vraagt de vraag om toestemming op elke systeem eigen client (Desktop/mobiele app) om toestemming te verlenen is het beoogde gedrag. De systeem eigen client identiteit is inherent onveilig (in tegens telling tot de vertrouwelijke client toepassing die een geheim uitwisselt met het micro soft-identiteits platform om hun identiteit te bewijzen). Het micro soft Identity-platform heeft ervoor gekozen om deze inbeveiliging te beperken voor consumenten Services door de gebruiker om toestemming te vragen, telkens wanneer de toepassing wordt geautoriseerd.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
