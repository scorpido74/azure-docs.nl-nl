---
title: SSO configureren op macOS en iOS
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van één aanmelding (SSO) op macOS en iOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881245"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>How to: SSO configureren op macOS en iOS

De Microsoft Authentication Library (MSAL) voor macOS en iOS ondersteunt Single Sign-on (SSO) tussen macOS/iOS-apps en browsers. In dit artikel worden de volgende SSO-scenario's bewerk:

- [Stille SSO tussen meerdere apps](#silent-sso-between-apps)

Dit type SSO werkt tussen meerdere apps die door dezelfde Apple Developer worden gedistribueerd. Het biedt stille SSO (dat wil zeggen, de gebruiker wordt niet gevraagd om referenties) door het lezen van refresh tokens geschreven door andere apps uit de sleutelhanger, en ze in stilte te ruilen voor toegangstokens.  

- [SSO via verificatiemakelaar](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Deze flow is niet beschikbaar op macOS.

Microsoft biedt apps, zogenaamde brokers, waarmee SSO tussen toepassingen van verschillende leveranciers mogelijk is, zolang het mobiele apparaat is geregistreerd bij Azure Active Directory (AAD). Dit type SSO vereist een broker-toepassing die op het apparaat van de gebruiker wordt geïnstalleerd.

- **SSO tussen MSAL en Safari**

SSO wordt bereikt via de [klasse ASWebAuthenticationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Het maakt gebruik van bestaande aanmeldingsstatus van andere apps en de Safari-browser. Het is niet beperkt tot apps die door dezelfde Apple Developer worden gedistribueerd, maar het vereist enige interactie van de gebruiker.

Als u de standaardwebweergave in uw app gebruikt om gebruikers aan te melden, krijgt u automatische SSO tussen MSAL-gebaseerde toepassingen en Safari. Ga naar [Browsers en WebViews aanpassen voor](customize-webviews.md)meer informatie over de webweergaven die MSAL ondersteunt.

> [!IMPORTANT]
> Dit type SSO is momenteel niet beschikbaar op macOS. MSAL op macOS ondersteunt alleen WKWebView, dat geen SSO-ondersteuning heeft met Safari. 

- **Stille SSO tussen ADAL en MSAL macOS/iOS apps**

MSAL Objective-C ondersteunt migratie en SSO met ADAL Objective-C-gebaseerde apps. De apps moeten worden gedistribueerd door dezelfde Apple Developer.

Zie [SSO tussen ADAL- en MSAL-apps op macOS en iOS](sso-between-adal-msal-apps-macos-ios.md) voor instructies voor cross-app SSO tussen ADAL- en MSAL-gebaseerde apps.

## <a name="silent-sso-between-apps"></a>Stille SSO tussen apps

MSAL ondersteunt SSO-delen via iOS-sleutelhangertoegangsgroepen.

Als u SSO voor uw toepassingen wilt inschakelen, moet u de volgende stappen uitvoeren, die hieronder nader worden uitgelegd:

1. Zorg ervoor dat al uw toepassingen dezelfde client-id of toepassings-id gebruiken.
1. Zorg ervoor dat al uw toepassingen hetzelfde ondertekeningscertificaat van Apple delen, zodat u sleutelhangers delen.
1. Vraag voor elk van uw toepassingen hetzelfde sleutelketenrecht aan.
1. Vertel de MSAL SDKs over de gedeelde sleutelhanger die u wilt dat we gebruiken als deze anders is dan de standaardketen.

### <a name="use-the-same-client-id-and-application-id"></a>Dezelfde client-id en toepassings-id gebruiken

Als het Microsoft-identiteitsplatform wil weten welke toepassingen tokens kunnen delen, moeten deze toepassingen dezelfde client-id of toepassings-id delen. Dit is de unieke id die u werd verstrekt toen u uw eerste toepassing in de portal registreerde.

De manier waarop het Microsoft-identiteitsplatform apps vertelt die dezelfde toepassings-id apart gebruiken, is door hun **Omleidings-URI's.** Elke toepassing kan meerdere Omleidings-URI's hebben die zijn geregistreerd in de onboarding-portal. Elke app in uw suite heeft een andere omleiding URI. Bijvoorbeeld:

App1 Redirect URI:`msauth.com.contoso.mytestapp1://auth`  
App2 Redirect URI:`msauth.com.contoso.mytestapp2://auth`  
App3 Redirect URI:`msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Het formaat van omleidingsuris moet compatibel zijn met het formaat dat MSAL ondersteunt, dat is gedocumenteerd in [MSAL Redirect URI-formaatvereisten](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Het delen van sleutelhangers instellen tussen toepassingen

Raadpleeg het artikel ['Toevoegen van Apple'](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) om het delen van sleutelhangers mogelijk te maken. Wat belangrijk is, is dat u beslist hoe u wilt dat uw sleutelhanger wordt aangeroepen en dat vermogen toevoegt aan al uw toepassingen die betrokken zullen zijn bij SSO.

Wanneer u de rechten correct hebt ingesteld, ziet `entitlements.plist` u een bestand in uw projectmap dat iets als dit voorbeeld bevat:

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

#### <a name="add-a-new-keychain-group"></a>Een nieuwe sleutelhangergroep toevoegen

Voeg een nieuwe sleutelhangergroep toe aan uw **projectmogelijkheden.** De sleutelhangergroep moet zijn:
* `com.microsoft.adalcache`op iOS 
* `com.microsoft.identity.universalstorage`op macOS.

![voorbeeld sleutelhanger](media/single-sign-on-macos-ios/keychain-example.png)

Zie [sleutelhangergroepen voor](howto-v2-keychain-objc.md)meer informatie.

## <a name="configure-the-application-object"></a>Het toepassingsobject configureren

Zodra u het keychain-recht hebt ingeschakeld in elk van uw toepassingen en `MSALPublicClientApplication` u klaar bent om SSO te gebruiken, configureert u met uw sleutelhangertoegangsgroep zoals in het volgende voorbeeld:

Doelstelling-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

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
> Wanneer u een sleutelhanger deelt in uw toepassingen, kan elke toepassing gebruikers of zelfs alle tokens in uw toepassing verwijderen.
> Dit is vooral impactvol als u toepassingen hebt die afhankelijk zijn van tokens om achtergrondwerk te doen.
> Het delen van een sleutelhanger betekent dat u heel voorzichtig moet zijn wanneer uw app microsoft-identiteitsSDK-verwijderingsbewerkingen gebruikt.

Dat is alles. De Microsoft identity SDK deelt nu referenties voor al uw toepassingen. De accountlijst wordt ook gedeeld in toepassingsinstanties.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO via Authenticatie broker op iOS

MSAL biedt ondersteuning voor gebrokereerde verificatie met Microsoft Authenticator. Microsoft Authenticator biedt SSO voor aad-geregistreerde apparaten en helpt uw toepassing ook bij het volgen van beleid voor voorwaardelijke toegang.

De volgende stappen zijn hoe u SSO inschakelt met behulp van een verificatiebroker voor uw app:

1. Registreer een broker compatibele Redirect URI-indeling voor de toepassing in de Info.plist van uw app. De broker compatibele Redirect `msauth.<app.bundle.id>://auth`URI-indeling is . Vervang '<app.bundle.id>'' door de bundel-ID van uw toepassing. Bijvoorbeeld:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Voeg de volgende schema's toe aan `LSApplicationQueriesSchemes`de Info.plist van uw app onder :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Voeg het volgende `AppDelegate.m` toe aan uw bestand om callbacks af te handelen:

    Doelstelling-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Als u Xcode 11 gebruikt,** moet u in `SceneDelegate` plaats daarvan MSAL-callback in het bestand plaatsen.
Als u zowel UISceneDelegate als UIApplicationDelegate ondersteunt voor compatibiliteit met oudere iOS, moet MSAL-terugroepen in beide bestanden worden geplaatst.

Doelstelling-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

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

Meer informatie over [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)