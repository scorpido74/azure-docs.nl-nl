---
title: Eenmalige aanmelding configureren in macOS en iOS | Micro soft Identity-platform
description: Meer informatie over het configureren van eenmalige aanmelding (SSO) in macOS en iOS.
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
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a407b57a380d059703383b02e37decb8761786f4
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268932"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Procedure: Eenmalige aanmelding configureren in macOS en iOS

De micro soft Authentication Library (MSAL) voor macOS en iOS ondersteunt eenmalige aanmelding (SSO) tussen macOS/iOS-apps en-browsers. In dit artikel worden de volgende SSO-scenario's behandeld:

- [Stille SSO tussen meerdere apps](#silent-sso-between-apps)

Dit type SSO werkt tussen meerdere apps die door dezelfde Apple-ontwikkelaar worden gedistribueerd. Het biedt Silent SSO (dat wil zeggen, de gebruiker wordt niet gevraagd om referenties) door vernieuwings tokens te lezen die zijn geschreven door andere apps uit de sleutel hanger, en ze te wisselen voor toegangs tokens op de achtergrond.  

- [SSO via verificatie Broker](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Deze stroom is niet beschikbaar in macOS.

Micro soft biedt apps, die Brokers worden genoemd, die SSO mogelijk maken tussen toepassingen van verschillende leveranciers zolang het mobiele apparaat is geregistreerd met Azure Active Directory (AAD). Voor dit type SSO moet een Broker-toepassing worden geïnstalleerd op het apparaat van de gebruiker.

- **SSO tussen MSAL en Safari**

SSO wordt bereikt via de [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) -klasse. Er wordt een bestaande aanmeldings status van andere apps en de Safari-browser gebruikt. Het is niet beperkt tot apps die worden gedistribueerd door dezelfde Apple-ontwikkelaar, maar hiervoor is wel een interactie van de gebruiker vereist.

Als u de standaard webweergave in uw app gebruikt om gebruikers aan te melden, ontvangt u automatische SSO tussen MSAL toepassingen en Safari. Ga voor meer informatie over de webweergaven die MSAL ondersteunt naar [Customize browsers and Webviews](customize-webviews.md).

> [!IMPORTANT]
> Dit type SSO is momenteel niet beschikbaar in macOS. MSAL in macOS ondersteunt alleen WKWebView die geen SSO-ondersteuning met Safari hebben. 

- **Stille SSO tussen ADAL en MSAL macOS/iOS-apps**

MSAL doelstelling-C ondersteunt migratie en eenmalige aanmelding met ADAL-toepassingen op basis van het doel C. De apps moeten worden gedistribueerd door dezelfde Apple-ontwikkelaar.

Zie [SSO tussen ADAL-en MSAL-apps in macOS en IOS](sso-between-adal-msal-apps-macos-ios.md) voor instructies voor de cross-app-SSO tussen ADAL-en MSAL-apps.

## <a name="silent-sso-between-apps"></a>Stille SSO tussen apps

MSAL biedt ondersteuning voor het delen van SSO via iOS-toegangs groepen voor sleutel hanger.

Als u SSO in uw toepassingen wilt inschakelen, moet u de volgende stappen uitvoeren, die in meer detail worden beschreven:

1. Zorg ervoor dat alle toepassingen dezelfde client-ID of toepassings-ID gebruiken.
1. Zorg ervoor dat alle toepassingen hetzelfde handtekening certificaat van Apple delen, zodat u de sleutel ketens kunt delen.
1. Vraag hetzelfde sleutel hanger aan voor elk van uw toepassingen.
1. Vertel de MSAL-Sdk's over de gedeelde sleutel hanger die u door ons wilt laten gebruiken als deze verschilt van de standaard versie.

### <a name="use-the-same-client-id-and-application-id"></a>Dezelfde client-ID en toepassings-ID gebruiken

Voor het micro soft Identity-platform om te weten welke toepassingen tokens kunnen delen, moeten die toepassingen dezelfde client-ID of toepassings-ID delen. Dit is de unieke id die aan u is gegeven tijdens het registreren van uw eerste toepassing in de portal.

De manier waarop het micro soft Identity-platform voor apps die gebruikmaken van dezelfde toepassings-ID, wordt door de **omleidings-uri's**verteld. Voor elke toepassing kunnen meerdere omleidings-Uri's zijn geregistreerd in de onboarding-Portal. Elke app in uw suite krijgt een andere omleidings-URI. Bijvoorbeeld:

App1 omleidings-URI: `msauth.com.contoso.mytestapp1://auth`App2 omleidings-URI: `msauth.com.contoso.mytestapp2://auth`App3 omleidings-URI:`msauth.com.contoso.mytestapp3://auth`

> [!IMPORTANT]
> De indeling van omleidings-uri's moet compatibel zijn met de indeling MSAL ondersteunt, die wordt beschreven in de [vereisten voor MSAL omleidings-URI-indeling](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Sleutel hanger delen tussen toepassingen instellen

Raadpleeg het artikel ' [functies toevoegen](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) ' van Apple om het delen van sleutel hanger in te scha kelen. Het is belang rijk dat u bepaalt wat u wilt dat uw sleutel keten wordt aangeroepen en voeg die mogelijkheid toe aan alle toepassingen die bij SSO betrokken zullen worden.

Wanneer u de rechten op de juiste manier hebt ingesteld, ziet u een `entitlements.plist` bestand in de projectmap dat iets als dit voor beeld bevat:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Wanneer u de sleutel hanger hebt ingeschakeld in elk van uw toepassingen en u klaar bent voor het gebruik van SSO `MSALPublicClientApplication` , configureert u met de toegangs groep voor de sleutel hanger, zoals in het volgende voor beeld:

Doel-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"
        
do {
    let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



> [!WARNING]
> Wanneer u een sleutel hanger in uw toepassingen deelt, kan elke toepassing gebruikers of zelfs alle tokens in uw toepassing verwijderen.
> Dit is vooral van invloed op toepassingen die afhankelijk zijn van tokens om achtergrond taken uit te voeren.
> Het delen van een sleutel hanger houdt in dat u zeer voorzichtig moet zijn wanneer uw app gebruikmaakt van micro soft Identity SDK Remove-bewerkingen.

Dat is alles. De micro soft Identity SDK zal nu referenties delen in al uw toepassingen. De lijst met accounts wordt ook gedeeld tussen toepassings exemplaren.

## <a name="sso-through-authentication-broker-on-ios"></a>Eenmalige aanmelding via de verificatie Broker op iOS

MSAL biedt ondersteuning voor brokered-verificatie met Microsoft Authenticator. Microsoft Authenticator biedt eenmalige aanmelding voor AAD-geregistreerde apparaten en helpt uw toepassing bij het volgen van beleid voor voorwaardelijke toegang.

Met de volgende stappen kunt u eenmalige aanmelding inschakelen met behulp van een verificatie Broker voor uw app:

1. Registreer een Broker-compatibele omleidings-URI-indeling voor de toepassing in de info. plist van uw app. De indeling van de Broker-compatibele `msauth.<app.bundle.id>://auth`omleidings-URI is. Vervang ' < app. bundel. id > ' ' door de bundel-ID van uw toepassing. Bijvoorbeeld:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Voeg volgende schema's toe aan de info. plist van uw `LSApplicationQueriesSchemes`app onder:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Voeg het volgende toe aan `AppDelegate.m` het bestand voor het afhandelen van retour aanroepen:

    Doel-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Als u Xcode 11 gebruikt**, moet u in plaats daarvan MSAL-Call `SceneDelegate` back in het bestand plaatsen.
Als u zowel UISceneDelegate als UIApplicationDelegate voor compatibiliteit met oudere iOS-versies ondersteunt, moet MSAL call back in beide bestanden worden geplaatst.

Doel-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```
    
## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)
