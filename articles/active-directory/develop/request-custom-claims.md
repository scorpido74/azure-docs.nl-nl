---
title: Aangepaste claims aanvragen (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanvragen van aangepaste claims.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4974fe3b387683f662d7a7b4f3ccb4935153f07e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883093"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>How to: Aangepaste claims aanvragen met MSAL voor iOS en macOS

Met OpenID Connect u optioneel de terugkeer van individuele claims aanvragen bij het UserInfo-eindpunt en/of in het ID-token. Een claimaanvraag wordt weergegeven als een JSON-object dat een lijst met aanvragen bevat. Zie [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) voor meer informatie.

De Microsoft Authentication Library (MSAL) voor iOS en macOS maakt het aanvragen van specifieke claims mogelijk in zowel interactieve als stille tokenacquisitiescenario's. Het doet dit `claimsRequest` via de parameter.

Er zijn meerdere scenario's waarin dit nodig is. Bijvoorbeeld:

- Het aanvragen van claims buiten de standaardset voor uw aanvraag.
- Het aanvragen van specifieke combinaties van de standaardclaims die niet kunnen worden opgegeven met behulp van scopes voor uw toepassing. Als een toegangstoken bijvoorbeeld wordt geweigerd vanwege ontbrekende claims, kan de toepassing de ontbrekende claims aanvragen met MSAL.

> [!NOTE]
> MSAL omzeilt de cache van het toegangstoken wanneer een claimaanvraag is opgegeven. Het is belangrijk om `claimsRequest` alleen parameter te bieden wanneer aanvullende `claimsRequest` claims nodig zijn (in tegenstelling tot altijd dezelfde parameter in elke MSAL API-aanroep).

`claimsRequest`kunnen worden `MSALSilentTokenParameters` gespecificeerd `MSALInteractiveTokenParameters`in en :

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`kan worden opgebouwd uit een NSString-weergave van JSON-claimaanvragen. 

Doelstelling-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



Het kan ook worden gewijzigd door aanvullende specifieke claims aan te vragen:

Doelstelling-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest`moet vervolgens worden ingesteld in de tokenparameters en worden verstrekt aan een van de API's voor MSAL-tokenacquisities:

Doelstelling-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)
