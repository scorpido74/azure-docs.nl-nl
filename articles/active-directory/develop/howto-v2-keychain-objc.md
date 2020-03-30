---
title: Sleutelketen configureren
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van sleutelhangers zodat uw app tokens in de sleutelhanger kan incachen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085862"
---
# <a name="configure-keychain"></a>Sleutelketen configureren

Wanneer de [Microsoft-verificatiebibliotheek voor iOS en macOS](msal-overview.md) (MSAL) een gebruiker ondertekent of een token vernieuwt, probeert deze tokens in de sleutelhanger in de cache te plaatsen. Met caching-tokens in de sleutelhanger kan MSAL stille single sign-on (SSO) bieden tussen meerdere apps die worden gedistribueerd door dezelfde Apple-ontwikkelaar. SSO wordt bereikt via de functionaliteit van de sleutelhangers. Zie de [documentatie Keychain Items van](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)Apple voor meer informatie .

In dit artikel wordt uitgelegd hoe u app-rechten configureren, zodat MSAL tokens in de cache kan schrijven naar iOS- en macOS-sleutelhangers.

## <a name="default-keychain-access-group"></a>Standaardgroep voor sleutelhangertoegang

### <a name="ios"></a>iOS

MSAL op iOS `com.microsoft.adalcache` maakt standaard gebruik van de toegangsgroep. Dit is de gedeelde toegangsgroep die wordt gebruikt door zowel DE) MSAL- als Azure AD-verificatiebibliotheek (ADAL) SDK's en zorgt voor de beste single sign-on (SSO)-ervaring tussen meerdere apps van dezelfde uitgever.

Voeg op iOS `com.microsoft.adalcache` de sleutelhangergroep toe aan het recht van uw app in XCode onder **Projectinstellingen** > **Mogelijkheden** > **Sleutelhangerdelen**

### <a name="macos"></a>macOS

MSAL op macOS maakt standaard gebruik van `com.microsoft.identity.universalstorage` toegangsgroep.

Vanwege macOS-sleutelhangerbeperkingen vertaalt MSAL's `access group` niet direct naar het kenmerk sleutelhangertoegangsgroep (zie [kSecAttrAccessGroup)](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)op macOS 10.14 en eerder. Echter, het gedraagt zich op dezelfde manier vanuit een SSO perspectief door ervoor te zorgen dat meerdere toepassingen gedistribueerd door dezelfde Apple ontwikkelaar kan stille SSO hebben.

Op macOS 10.15 (macOS Catalina) gebruikt MSAL het kenmerk sleutelhangertoegang om stille SSO te bereiken, vergelijkbaar met iOS.

## <a name="custom-keychain-access-group"></a>Aangepaste sleutelhangertoegangsgroep

Als u een andere sleutelhangertoegangsgroep wilt gebruiken, u uw `MSALPublicClientApplicationConfig` aangepaste `MSALPublicClientApplication`groep doorgeven wanneer u deze maakt voordat u dit maakt, zoals dit:

# <a name="objective-c"></a>[Doelstelling-C](#tab/objc)

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

# <a name="swift"></a>[Swift](#tab/swift)

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

---

## <a name="disable-keychain-sharing"></a>Delen van sleutelhangers uitschakelen

Als u de SSO-status niet wilt delen tussen meerdere apps of een sleutelhangerwilt gebruiken, schakelt u het delen van sleutelhangers uit door de toepassingsbundel-id als sleutelhanger-groep door te geven:

# <a name="objective-c"></a>[Doelstelling-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Fout verwerken bij het afhandelen van -34018 (kan item niet instellen in sleutelhanger)

Fout -34018 betekent normaal gesproken dat de sleutelhanger niet correct is geconfigureerd. Zorg ervoor dat de sleutelhangertoegangsgroep die is geconfigureerd in MSAL overeenkomt met de groep die is geconfigureerd in rechten.

## <a name="ensure-your-application-is-properly-signed"></a>Zorg ervoor dat uw aanvraag correct is ondertekend

Op macOS kunnen toepassingen worden uitgevoerd zonder te zijn ondertekend door de ontwikkelaar. Hoewel de meeste functionaliteit van MSAL blijft werken, vereist SSO via sleutelhangertoegang dat de toepassing moet worden ondertekend. Als u meerdere sleutelhangerprompts ondervindt, controleert u of de handtekening van uw toepassing geldig is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over toegangsgroepen voor sleutelhangers in apple's [Toegang tot sleutelhangeritems Onder een](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) artikel over verzameling apps.
