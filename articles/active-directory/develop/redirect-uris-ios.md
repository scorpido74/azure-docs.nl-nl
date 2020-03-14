---
title: Omleidings-Uri's gebruiken met MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen de micro soft-verificatie bibliotheek voor ObjectiveC (MSAL voor iOS en macOS) en de Azure AD-verificatie bibliotheek voor ObjectiveC (ADAL. ObjC) en hoe u ertussen kunt migreren.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: c36c6b1b1b08de6d2db9a7f7f9ebd3b162c02383
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240840"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Omleidings-Uri's gebruiken met de micro soft-verificatie bibliotheek voor iOS en macOS

Wanneer een gebruiker zich verifieert, stuurt Azure Active Directory (Azure AD) het token naar de app met behulp van de omleidings-URI die is geregistreerd bij de Azure AD-toepassing.

Voor de micro soft Authentication Library (MSAL) moet de omleidings-URI in een specifieke indeling zijn geregistreerd bij de Azure AD-app. MSAL maakt gebruik van een standaard omleidings-URI als u er geen opgeeft. De indeling is `msauth.[Your_Bundle_Id]://auth`.

De standaard-omleidings-URI-indeling werkt voor de meeste apps en scenario's, waaronder brokered-verificatie en systeem webweergave. Gebruik zo mogelijk de standaard indeling.

Het is echter mogelijk dat u de omleidings-URI voor geavanceerde scenario's moet wijzigen, zoals hieronder wordt beschreven.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scenario's waarvoor een andere omleidings-URI is vereist

### <a name="cross-app-single-sign-on-sso"></a>Eenmalige aanmelding tussen apps (SSO)

Voor het micro soft-identiteits platform voor het delen van tokens in apps moet elke app dezelfde client-ID of toepassings-ID hebben. Dit is de unieke id die wordt gegeven wanneer u uw app in de portal hebt geregistreerd (niet de Application bundel-ID die u per app met Apple registreert).

De omleidings-Uri's moeten voor elke iOS-app verschillend zijn. Hierdoor kan de micro soft Identity-service verschillende apps identificeren die een toepassings-ID delen. Voor elke toepassing kunnen meerdere omleidings-Uri's zijn geregistreerd in de Azure Portal. Elke app in uw suite krijgt een andere omleidings-URI. Bijvoorbeeld:

Op basis van de volgende registratie van de toepassing in de Azure Portal:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 maakt gebruik van Redirect `msauth.com.contoso.app1://auth` App2 gebruikt `msauth.com.contoso.app2://auth` App3 gebruikt `msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migreren van ADAL naar MSAL

Bij het migreren van code die de Azure AD-verificatie bibliotheek (ADAL) heeft gebruikt voor MSAL, hebt u mogelijk al een omleidings-URI geconfigureerd voor uw app. U kunt dezelfde omleidings-URI blijven gebruiken als uw ADAL-app is geconfigureerd om brokered scenario's te ondersteunen en de omleidings-URI voldoet aan de vereisten voor de MSAL omleidings-URI-indeling.

## <a name="msal-redirect-uri-format-requirements"></a>Vereisten voor MSAL-omleidings-URI-indeling

* De MSAL-omleidings-URI moet de vorm `<scheme>://host`

    Waarbij `<scheme>` een unieke teken reeks is waarmee uw app wordt geïdentificeerd. Het is voornamelijk gebaseerd op de bundel-id van uw toepassing om uniekheid te garanderen. Als de bundel-ID van uw app bijvoorbeeld is `com.contoso.myapp`, heeft de omleidings-URI de volgende vorm: `msauth.com.contoso.myapp://auth`.

    Als u migreert vanaf ADAL, heeft de omleidings-URI waarschijnlijk de volgende indeling: `<scheme>://[Your_Bundle_Id]`, waarbij `scheme` een unieke teken reeks is. Deze indeling blijft werken wanneer u MSAL gebruikt.

* `<scheme>` moeten worden geregistreerd in de info. plist van uw app onder `CFBundleURLTypes > CFBundleURLSchemes`.  In dit voor beeld is info. plist geopend als bron code:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```
    

MSAL controleert of de omleidings-URI correct wordt geregistreerd en retourneert een fout als dat niet het geval is.
    
* Als u universele koppelingen wilt gebruiken als omleidings-URI, moet de `<scheme>` `https` zijn en niet hoeven te worden gedeclareerd in `CFBundleURLSchemes`. In plaats daarvan configureert u de app en het domein per Apple-instructies in [universele koppelingen voor ontwikkel aars](https://developer.apple.com/ios/universal-links/) en roept u de `handleMSALResponse:sourceApplication:` methode van `MSALPublicClientApplication` aan wanneer uw toepassing wordt geopend via een universele koppeling.

## <a name="use-a-custom-redirect-uri"></a>Een aangepaste omleidings-URI gebruiken

Als u een aangepaste omleidings-URI wilt gebruiken, geeft u de para meter `redirectUri` door aan `MSALPublicClientApplicationConfig` en geeft u dat object door aan `MSALPublicClientApplication` wanneer u het object initialiseert. Als de omleidings-URI ongeldig is, retourneert de initialisatie functie `nil` en wordt de `redirectURIError`ingesteld met aanvullende informatie.  Bijvoorbeeld:

Doel-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>De gebeurtenis URL geopend verwerken

Uw toepassing moet MSAL aanroepen wanneer deze een reactie ontvangt via URL-schema's of universele koppelingen. Roep de `handleMSALResponse:sourceApplication:` methode van `MSALPublicClientApplication` aan wanneer uw toepassing wordt geopend. Hier volgt een voor beeld van aangepaste schema's:

Doel-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)
