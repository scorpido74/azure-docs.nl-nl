---
title: Sleutelketen configureren
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van sleutel ketens, zodat uw app tokens in de sleutel hanger kan opslaan in de cache.
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69991d105ff3523310f54e65596f2f379b547052
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803804"
---
# <a name="configure-keychain"></a>Sleutelketen configureren

Wanneer de [micro soft-verificatie bibliotheek voor IOS-en macOS](msal-overview.md) (MSAL) in een gebruiker of een token vernieuwt, probeert de tokens in de sleutel hanger op te slaan. In cache-tokens in de sleutel hanger kan MSAL eenmalige aanmelding (SSO) bieden tussen meerdere apps die worden gedistribueerd door dezelfde Apple-ontwikkelaar. SSO wordt bereikt via de functionaliteit voor de toegangs groepen van de sleutel hanger. Zie de documentatie voor de [sleutel hanger-items](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)van Apple voor meer informatie.

In dit artikel wordt beschreven hoe u de rechten van een app kunt configureren, zodat MSAL tokens in de cache kan schrijven naar iOS-en macOS-sleutel hanger.

## <a name="default-keychain-access-group"></a>Standaard toegangs groep voor sleutel hanger

### <a name="ios"></a>iOS

MSAL op iOS maakt standaard gebruik van de `com.microsoft.adalcache`-toegangs groep. Dit is de gedeelde toegangs groep die wordt gebruikt door zowel de MSAL-als de Azure AD Authentication Library (ADAL) Sdk's en zorgt voor de beste SSO-ervaring (eenmalige aanmelding) tussen meerdere apps van dezelfde uitgever.

Voeg op iOS de `com.microsoft.adalcache`-sleutel keten groep toe aan het recht van uw app in XCode onder **project instellingen** > **mogelijkheden** > **sleutel hanger delen**

### <a name="macos"></a>macOS

MSAL in macOS maakt standaard gebruik van `com.microsoft.identity.universalstorage`-toegangs groep.

Als gevolg van de beperkingen van de macOS-sleutel hanger de `access group` van MSAL niet rechtstreeks naar het kenmerk voor de toegangs groep van de sleutel hanger (Zie [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) op macOS 10,14 en eerdere versies. Het werkt echter op dezelfde manier als een SSO-perspectief door ervoor te zorgen dat meerdere toepassingen die door dezelfde Apple-ontwikkelaar worden gedistribueerd, Silent SSO kunnen hebben.

Op macOS 10,15 en hoger (macOS Catalina) maakt MSAL gebruik van de toegangs groeps kenmerk sleutel hanger voor het verkrijgen van een stille SSO, op dezelfde wijze als iOS.

## <a name="custom-keychain-access-group"></a>Toegangs groep voor aangepaste sleutel hanger

Als u een andere toegangs groep voor de sleutel hanger wilt gebruiken, kunt u uw aangepaste groep door geven tijdens het maken van `MSALPublicClientApplicationConfig` voordat u `MSALPublicClientApplication` maakt, bijvoorbeeld:

Doel-C:

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```



Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="disable-keychain-sharing"></a>Sleutel hanger delen uitschakelen

Als u de SSO-status niet wilt delen tussen meerdere apps of een toegangs groep voor de sleutel hanger wilt gebruiken, moet u het delen van sleutel hanger uitschakelen door de Application bundel-ID als uw keychainGroup door te geven:

Doel-C:

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

Swift

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Handle-34018-fout (kan item niet in sleutel hanger instellen)

Fout-34018 betekent normaal gesp roken dat de sleutel hanger niet juist is geconfigureerd. Zorg ervoor dat de toegangs groep voor de sleutel hanger die is geconfigureerd in MSAL overeenkomt met de keten die is geconfigureerd in rechten.

## <a name="ensure-your-application-is-properly-signed"></a>Zorg ervoor dat uw toepassing correct is ondertekend

In macOS kunnen toepassingen worden uitgevoerd zonder te worden ondertekend door de ontwikkelaar. Hoewel de meeste functionaliteit van MSAL blijft werken, moet de SSO via de toegang tot de sleutel hanger worden ondertekend. Als u meerdere sleutel Hangers vraagt, moet u ervoor zorgen dat de hand tekening van uw toepassing geldig is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over toegangs groepen voor sleutel hanger in Apple [delen toegang tot sleutel keten items in een verzameling van apps-artikelen](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) .
