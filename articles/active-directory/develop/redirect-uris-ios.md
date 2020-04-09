---
title: Omleidings-URI's gebruiken met MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen Microsoft Authentication Library for ObjectiveC (MSAL voor iOS en macOS) en Azure AD Authentication Library for ObjectiveC (ADAL. ObjC) en hoe te migreren tussen hen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 1291563a39e3cf3acd4b343302be8b150bf794ca
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883505"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Omleidings-URI's gebruiken met de Microsoft-verificatiebibliotheek voor iOS en macOS

Wanneer een gebruiker zich verifieert, stuurt Azure Active Directory (Azure AD) het token naar de app met behulp van de omleiding uri die is geregistreerd met de Azure AD-toepassing.

De Microsoft Authentication-bibliotheek (MSAL) vereist dat de omleidinguri wordt geregistreerd met de Azure AD-app in een specifieke indeling. MSAL gebruikt een standaard omleiding URI, als u er geen opgeeft. De indeling is `msauth.[Your_Bundle_Id]://auth`.

De standaard omleidinguri-indeling werkt voor de meeste apps en scenario's, inclusief gebrokereerde verificatie en systeemwebweergave. Gebruik de standaardindeling waar mogelijk.

Het kan echter nodig zijn om de omleiding URI te wijzigen voor geavanceerde scenario's, zoals hieronder beschreven.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scenario's waarvoor een andere omleiding suri vereist

### <a name="cross-app-single-sign-on-sso"></a>Cross-app enkelteken op (SSO)

Als het Microsoft Identity-platform tokens kan delen tussen apps, moet elke app dezelfde client-id of toepassings-id hebben. Dit is de unieke id die wordt verstrekt toen u uw app in de portal registreerde (niet de applicatiebundel-id die u per app registreert bij Apple).

De omleidings-URI's moeten voor elke iOS-app anders zijn. Hierdoor kan de Microsoft-identiteitsservice op unieke wijze verschillende apps identificeren die een toepassings-id delen. Elke toepassing kan meerdere omleidings-URI's laten registreren in de Azure-portal. Elke app in uw suite heeft een andere omleiding URI. Bijvoorbeeld:

Gezien de volgende toepassingsregistratie in de Azure-portal:

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 maakt `msauth.com.contoso.app1://auth` gebruik `msauth.com.contoso.app2://auth` van redirect App2 gebruikt App3 gebruikt`msauth.com.contoso.app1://auth`

### <a name="migrating-from-adal-to-msal"></a>Migreren van ADAL naar MSAL

Wanneer u code migreert die de Azure AD-verificatiebibliotheek (ADAL) naar MSAL heeft gebruikt, u al een omleidingsURI configureren voor uw app. U doorgaan met het gebruik van dezelfde omleiding URI zolang uw ADAL-app is geconfigureerd om brokered scenario's te ondersteunen en uw omleiding URI voldoet aan de MSAL redirect URI-indeling eisen.

## <a name="msal-redirect-uri-format-requirements"></a>MSAL redirect URI-formaat vereisten

* De MSAL redirect URI moet in het formulier`<scheme>://host`

    Waar `<scheme>` is een unieke tekenreeks die uw app identificeert. Het is voornamelijk gebaseerd op de bundel-id van uw toepassing om uniciteit te garanderen. Als de bundel-id van uw `com.contoso.myapp`app bijvoorbeeld is, bevindt `msauth.com.contoso.myapp://auth`u uri omleiden zich in het formulier: .

    Als u migreert vanuit ADAL, heeft uw omleidingsURI waarschijnlijk deze indeling: `<scheme>://[Your_Bundle_Id]`, waar `scheme` is een unieke tekenreeks. Deze indeling blijft werken wanneer u MSAL gebruikt.

* `<scheme>`moet zijn geregistreerd in de Info.plist van uw app onder `CFBundleURLTypes > CFBundleURLSchemes`.  In dit voorbeeld is Info.plist geopend als broncode:

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
    

MSAL controleert of uw uri-omleiding correct registreert en stuurt een foutmelding terug als dit niet het is.
    
* Als u universele links wilt gebruiken als `<scheme>` omleiding `https` URI, moet de worden `CFBundleURLSchemes`en hoeft niet te worden gedeclareerd in . Configureer in plaats daarvan de app en het domein volgens `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` instructies van Apple bij Universal Links [for Developers](https://developer.apple.com/ios/universal-links/) en roep de methode aan van wanneer uw toepassing wordt geopend via een universele koppeling.

## <a name="use-a-custom-redirect-uri"></a>Een aangepaste omleidings-URI gebruiken

Als u een aangepaste omleidingsURI wilt gebruiken, geeft u de `redirectUri` parameter door aan `MSALPublicClientApplicationConfig` en geeft u dat object door aan `MSALPublicClientApplication` wanneer u het object initialiseert. Als de omleiding URI ongeldig is, `nil` zal `redirectURIError`de initialisator terugkeren en de met aanvullende informatie instellen.  Bijvoorbeeld:

Doelstelling-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

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



## <a name="handle-the-url-opened-event"></a>De geopende URL-gebeurtenis afhandelen

Uw toepassing moet MSAL bellen wanneer het een antwoord ontvangt via URL-schema's of universele links. Roep `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` methode aan van wanneer uw toepassing wordt geopend. Hier is een voorbeeld voor aangepaste schema's:

Doelstelling-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)
