---
title: SSO tussen ADAL & MSAL apps (iOS/macOS) - Microsoft identity platform | Azure
description: ''
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 2fbb6e837ae898daf4bc78d5cccc75660463e8a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085415"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>How to: SSO tussen ADAL- en MSAL-apps op macOS en iOS

De Microsoft Authentication Library (MSAL) voor iOS kan de SSO-status delen met [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) tussen toepassingen. U uw apps in uw eigen tempo migreren naar MSAL, zodat uw gebruikers nog steeds profiteren van cross-app SSO - zelfs met een mix van ADAL- en MSAL-gebaseerde apps.

Als u op zoek bent naar richtlijnen voor het instellen van SSO tussen apps met de MSAL SDK, raadpleegt u [Silent SSO tussen meerdere apps.](single-sign-on-macos-ios.md#silent-sso-between-apps) Dit artikel richt zich op SSO tussen ADAL en MSAL.

De specifieke kenmerken van SSO zijn afhankelijk van de ADAL-versie die u gebruikt.

## <a name="adal-27x"></a>ADAL 2,7.x

Deze sectie behandelt SSO-verschillen tussen MSAL en ADAL 2.7.x

### <a name="cache-format"></a>Cache-indeling

ADAL 2.7.x kan de MSAL-cache-indeling lezen. U hoeft niets speciaals te doen voor cross-app SSO met versie ADAL 2.7.x. U moet zich echter bewust zijn van verschillen in account-id's die deze twee bibliotheken ondersteunen.

### <a name="account-identifier-differences"></a>Verschillen tussen account-id's

MSAL en ADAL gebruiken verschillende account-id's. ADAL gebruikt UPN als primaire account-id. MSAL gebruikt een niet-weergavebare account-id die is gebaseerd op een object-id en een tenant-id voor AAD-accounts en een `sub` claim voor andere typen accounts.

Wanneer u `MSALAccount` een object ontvangt in het MSAL-resultaat, bevat het een account-id in de `identifier` eigenschap. De toepassing moet deze id gebruiken voor volgende stille verzoeken.

Naast `identifier`, `MSALAccount` object bevat een displayable id genaamd `username`. Dat vertaalt `userId` zich in ADAL. `username`wordt niet beschouwd als een unieke id en kan op elk gewenst moment veranderen, dus het moet alleen worden gebruikt voor achterwaartse compatibiliteitsscenario's met ADAL. MSAL ondersteunt cachequery's `identifier`met behulp `identifier` van een of `username` andere query of wanneer query's worden aanbevolen.

Volgende tabel geeft een overzicht van de verschillen tussen ADAL en MSAL:

| Account-id                | MSAL                                                         | ADAL 2,7.x      | Ouder ADAL (vóór ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| weergeefbare id            | `username`                                                   | `userId`        | `userId`                       |
| unieke niet-weergavebare id | `identifier`                                                 | `homeAccountId` | N.v.t.                            |
| Geen account-id bekend               | Alle accounts `allAccounts:` opvragen via API in`MSALPublicClientApplication` | N.v.t.             | N.v.t.                            |

Dit is `MSALAccount` de interface die deze id's verstrekt:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO van MSAL naar ADAL

Als u een MSAL-app en een ADAL-app hebt en de gebruiker zich eerst aanmeldt bij de MSAL-app, u SSO in de ADAL-app krijgen door het `username` object op `MSALAccount` te slaan en door te geven aan uw ADAL-app als `userId`. ADAL kan de accountgegevens vervolgens `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` in stilte vinden met de API.

### <a name="sso-from-adal-to-msal"></a>SSO van ADAL naar MSAL

Als u een MSAL-app en een ADAL-app hebt en de gebruiker zich eerst aanmeldt bij de ADAL-app, u ADAL-gebruikers-id's gebruiken voor accountlookups in MSAL. Dit geldt ook bij het migreren van ADAL naar MSAL.

#### <a name="adals-homeaccountid"></a>ADAL's homeAccountId

ADAL 2.7.x `homeAccountId` retourneert het in het `ADUserInformation` object in het resultaat via deze eigenschap:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`in ADAL's is `identifier` gelijk aan in MSAL. U deze id opslaan om te gebruiken in `accountForIdentifier:error:` MSAL voor accountlookups met de API.

#### <a name="adals-userid"></a>ADAL's`userId`

Als `homeAccountId` er niet beschikbaar is of als u alleen de weergavebare id hebt, u adal's `userId` gebruiken om het account in MSAL op te zoeken.

Zoek in MSAL eerst een `username` `identifier`account op op of . Altijd `identifier` gebruiken voor het opvragen als `username` je het hebt, en alleen gebruiken als een terugval. Als het account wordt gevonden, `acquireTokenSilent` gebruikt u het account in de oproepen.

Doelstelling-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL ondersteunde api's voor accountlookup:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Deze sectie behandelt SSO-verschillen tussen MSAL en ADAL 2.x-2.6.6.

Oudere ADAL-versies ondersteunen niet native de MSAL-cache-indeling. Om een soepele migratie van ADAL naar MSAL te garanderen, kan MSAL echter de oudere ADAL-cache-indeling lezen zonder opnieuw om gebruikersreferenties te vragen.

Omdat `homeAccountId` deze niet beschikbaar is in oudere ADAL-versies, moet `username`u accounts opzoeken met behulp van:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Bijvoorbeeld:

Doelstelling-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



U ook alle accounts lezen, die ook accountgegevens van ADAL lezen:

Doelstelling-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)
