---
title: Mobiele apps configureren die web-Api's aanroepen-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept (de code configuratie van de app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d7148b104c723d124a954cf858ca77ff6552f94
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423795"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Mobiele app die web-Api's aanroept-code configuratie

Zodra u de toepassing hebt gemaakt, leert u hoe u de code kunt configureren met de para meters voor app-registratie. Mobiele toepassingen hebben ook enkele complexe specifiek, die te maken hebben met de aanpassing in het Framework voor het bouwen van deze apps

## <a name="msal-libraries-supporting-mobile-apps"></a>MSAL-bibliotheken die mobiele apps ondersteunen

De micro soft-bibliotheken die mobiele apps ondersteunen zijn:

  MSAL-bibliotheek | Beschrijving
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Voor het ontwikkelen van draag bare toepassingen. MSAL.NET ondersteunde platforms voor het bouwen van een mobiele toepassing zijn UWP, Xamarin. iOS en Xamarin. Android.
  ![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Systeem eigen iOS-toepassingen ontwikkelen met de doel stelling-C of SWIFT
  ![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Systeem eigen Android-toepassingen ontwikkelen in Java voor Android

## <a name="instantiating-the-application"></a>De toepassing instantiëren

### <a name="android"></a>Android

Mobiele toepassingen gebruiken de klasse `PublicClientApplication`. Hier kunt u een exemplaar maken:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobiele toepassingen op iOS moeten een exemplaar van de `MSALPublicClientApplication`-klasse maken.

Doel-C:

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

Er zijn [aanvullende MSALPublicClientApplicationConfig-eigenschappen](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) waarmee de standaard instantie kan worden overschreven, een omleidings-URI moet worden opgegeven of het cache gedrag voor het MSAL wordt gewijzigd. 

### <a name="xamarin-or-uwp"></a>Xamarin of UWP

In de volgende alinea wordt uitgelegd hoe u de toepassing kunt instantiëren voor Xamarin. iOS-, Xamarin. Android-en UWP-apps.

#### <a name="instantiating-the-application"></a>De toepassing instantiëren

In Xamarin, of UWP, is de eenvoudigste manier om de toepassing te instantiëren, waarbij de `ClientId` de GUID van de geregistreerde app is.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Er zijn aanvullende*para meters* voor het instellen van de gebruikers interface, het vervangen van de standaard instantie, het opgeven van een client naam en-versie (voor telemetrie), het opgeven van een omleidings-URI, de http-Factory opgeven die moet worden gebruikt (bijvoorbeeld voor het afhandelen van proxy's, telemetrie en logboek registratie opgeven). Dit is het onderwerp van de volgende alinea's.

##### <a name="specifying-the-parent-uiwindowactivity"></a>De bovenliggende UI/Window/activiteit opgeven

Op Android moet u de bovenliggende activiteit door geven voordat u interactieve verificatie doet. Wanneer u in iOS een Broker gebruikt, moet u de View Controller door geven. Op dezelfde manier kunt u in het bovenliggende venster het beste door geven aan UWP. Dit is mogelijk wanneer u het token aanschaft, maar het is ook mogelijk om een call back op te geven tijdens het maken van de app. een gemachtigde retourneert de UIParent.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

In Android raden we u aan de `CurrentActivityPlugin` [hier](https://github.com/jamesmontemagno/CurrentActivityPlugin)te gebruiken.  Vervolgens ziet uw `PublicClientApplication` Builder-code er als volgt uit:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Meer para meters voor het bouwen van apps

- Zie de referentie documentatie [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) voor een lijst met alle wijzigingen die op `PublicClientApplicationBuilder`beschikbaar zijn.
- Zie [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)in de referentie documentatie voor de beschrijving van alle opties die worden weer gegeven in `PublicClientApplicationOptions`.

## <a name="xamarin-ios-specific-considerations"></a>Specifieke overwegingen voor Xamarin iOS

Op Xamarin iOS zijn er verschillende overwegingen die u moet meenemen bij het gebruik van MSAL.NET:

1. [De functie `OpenUrl` in het `AppDelegate` overschrijven en implementeren](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Sleutel hanger groepen inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Het delen van token cache inschakelen](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Sleutel hanger toegang inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Details vindt u in [Xamarin IOS-overwegingen](msal-net-xamarin-ios-considerations.md)

## <a name="msal-for-ios-and-macos-specific-considerations"></a>MSAL voor iOS-en macOS-specifieke overwegingen

Vergelijk bare overwegingen zijn van toepassing wanneer u MSAL gebruikt voor iOS en macOS:

1. [De `openURL`-call back implementeren](#brokered-authentication-for-msal-for-ios-and-macos)
2. [Toegangs groepen voor sleutel hanger inschakelen](howto-v2-keychain-objc.md)
3. [Browsers en webweergaven aanpassen](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Specifieke aandachtspunten voor Xamarin Android

Hier zijn Xamarin Android-specifiek:

- [Controleren of het besturings element terugkeert naar MSAL zodra het interactieve deel van de verificatie stroom is beëindigd](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Het Android-manifest bijwerken](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [De Inge sloten webweergave gebruiken (optioneel)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Probleemoplossing](msal-net-xamarin-android-considerations.md#troubleshooting)

Details vindt u in [Xamarin Android-overwegingen](msal-net-xamarin-android-considerations.md)

Ten slotte zijn er enkele specifieke kenmerken die u moet weten over de browsers op Android. Deze worden beschreven in [Xamarin Android-specifieke overwegingen met MSAL.net](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Specifieke aandachtspunten voor UWP

Op UWP kunt u bedrijfs netwerken gebruiken. Zie [universeel Windows-platform-specifieke overwegingen met MSAL.net](msal-net-uwp-considerations.md)voor meer informatie over het gebruik van de MSAL-bibliotheek met UWP.

## <a name="configuring-the-application-to-use-the-broker"></a>De toepassing configureren voor het gebruik van de Broker

### <a name="why-use-brokers-in-ios-and-android-applications"></a>Waarom makelaars in iOS-en Android-toepassingen gebruiken?

Op Android en iOS bieden makelaars de volgende opties:

- Eenmalige aanmelding (SSO) wanneer het apparaat is geregistreerd bij AAD. Uw gebruikers hoeven zich niet aan te melden bij elke toepassing.
- Apparaat-id. Hiermee wordt het aan Azure AD-apparaat gekoppelde beleid voor voorwaardelijke toegang ingeschakeld door het certificaat van het apparaat te openen dat is gemaakt op het apparaat toen het was gekoppeld aan de werk plek.
- Verificatie van de toepassings-id. Wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven en wordt deze door de Broker gecontroleerd.

### <a name="enable-the-broker-on-xamarin"></a>De Broker inschakelen op Xamarin

Als u een van deze functies wilt inschakelen, gebruikt u de para meter `WithBroker()` wanneer u de `PublicClientApplicationBuilder.CreateApplication`-methode aanroept. `.WithBroker()` is standaard ingesteld op waar. Volg de onderstaande stappen voor [Xamarin. IOS](#brokered-authentication-for-xamarinios).

### <a name="enable-the-broker-for-msal-for-android"></a>De Broker inschakelen voor MSAL voor Android

Zie [brokered auth in Android](brokered-auth.md) voor meer informatie over het inschakelen van een Broker op Android. 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>De Broker inschakelen voor MSAL voor iOS en macOS

Brokered Authentication is standaard ingeschakeld voor AAD-scenario's in MSAL voor iOS en macOS. Volg de onderstaande stappen om uw toepassing te configureren voor ondersteuning voor brokered verificatie voor [MSAL voor IOS en macOS](#brokered-authentication-for-msal-for-ios-and-macos). Houd er rekening mee dat sommige stappen verschillen tussen [MSAL voor Xamarin. IOS](#brokered-authentication-for-xamarinios) en [MSAL voor IOS en macOS](#brokered-authentication-for-msal-for-ios-and-macos).

### <a name="brokered-authentication-for-xamarinios"></a>Brokered authenticatie voor Xamarin. iOS

Volg de onderstaande stappen om uw Xamarin. iOS-app in te scha kelen voor communicatie met de app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

#### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen

Broker-ondersteuning is ingeschakeld per`PublicClientApplication`. Het is standaard uitgeschakeld. U moet de para meter `WithBroker()` (standaard ingesteld op True) gebruiken bij het maken van de `PublicClientApplication` via de `PublicClientApplicationBuilder`.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Stap 2: AppDelegate bijwerken voor het afhandelen van de call back

Wanneer MSAL.NET de Broker aanroept, roept de Broker op zijn beurt terug naar uw toepassing via de `AppDelegate.OpenUrl` methode. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL.NET terug aan te roepen. U doet dit door het `AppDelegate.cs`-bestand bij te werken om de onderstaande methode te overschrijven.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Deze methode wordt aangeroepen telkens wanneer de toepassing wordt gestart en wordt gebruikt als een kans om de reactie van de Broker te verwerken en het verificatie proces te volt ooien dat door MSAL.NET wordt gestart.

#### <a name="step-3-set-a-uiviewcontroller"></a>Stap 3: een UIViewController () instellen

Met Xamarin iOS hoeft u normaal gesp roken geen object venster in te stellen, maar in dit geval kunt u antwoorden van een Broker verzenden en ontvangen. Stel in `AppDelegate.cs`nog een view controller in.

Ga als volgt te werk om het object venster in te stellen:

1) Stel in `AppDelegate.cs`de `App.RootViewController` in op een nieuwe `UIViewController()`. Dit zorgt ervoor dat er een `UIViewController` is met de aanroep naar de Broker. Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Op de AcquireTokenInteractive-aanroep gebruikt u de `.WithParentActivityOrWindow(App.RootViewController)` en geeft u de verwijzing naar het object venster dat u gaat gebruiken.

**Bijvoorbeeld:**

In `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
In `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
In de aanroep voor het verkrijgen van een token:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>Stap 4: een URL-schema registreren

MSAL.NET maakt gebruik van Url's om de Broker aan te roepen en retour neren het Broker-antwoord terug naar uw app. Als u de retour ronding wilt volt ooien, moet u een URL-schema voor uw app registreren in het `Info.plist`-bestand.

Voor voegsel van de `CFBundleURLSchemes` met `msauth`. Voeg vervolgens `CFBundleURLName` toe aan het einde.

`$"msauth.(BundleId)"`

**Bijvoorbeeld:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Dit URL-schema wordt onderdeel van de RedirectUri die wordt gebruikt voor het uniek identificeren van uw app bij het ontvangen van het antwoord van de Broker.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

#### <a name="step-5-lsapplicationqueriesschemes"></a>Stap 5: LSApplicationQueriesSchemes

MSAL maakt gebruik van `–canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 zijn de schema's vergrendeld waarvan een toepassing kan zoeken.

**Voeg** **`msauthv2`** toe aan de sectie `LSApplicationQueriesSchemes` van het `Info.plist` bestand.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Brokered authenticatie voor MSAL voor iOS en macOS

Brokered Authentication is standaard ingeschakeld voor AAD-scenario's.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Stap 1: AppDelegate bijwerken voor het afhandelen van de call back

Wanneer MSAL voor iOS en macOS de Broker aanroept, wordt de Broker op zijn beurt teruggebeld naar uw toepassing via de `openURL` methode. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL terug aan te roepen. U doet dit door het `AppDelegate.m`-bestand bij te werken om de onderstaande methode te overschrijven.

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
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Houd er rekening mee dat als u UISceneDelegate op iOS 13 + hebt aangenomen, MSAL call back moet worden geplaatst in de `scene:openURLContexts:` in plaats daarvan (Zie de [Apple-documentatie](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)). MSAL `handleMSALResponse:sourceApplication:` mag voor elke URL slechts eenmaal worden aangeroepen.

#### <a name="step-2-register-a-url-scheme"></a>Stap 2: een URL-schema registreren

MSAL voor iOS en macOS gebruikt Url's om de Broker aan te roepen en retour neren het Broker-antwoord terug naar uw app. Als u de retour ronding wilt volt ooien, moet u een URL-schema voor uw app registreren in het `Info.plist`-bestand.

Een voor voegsel voor uw aangepaste URL-schema met `msauth`. Voeg vervolgens **uw bundel-id** toe aan het einde.

`msauth.(BundleId)`

**Bijvoorbeeld:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Dit URL-schema wordt onderdeel van de RedirectUri die wordt gebruikt voor het uniek identificeren van uw app bij het ontvangen van het antwoord van de Broker. Zorg ervoor dat de RedirectUri in de indeling van `msauth.(BundleId)://auth` is geregistreerd voor uw toepassing in [Azure Portal](https://portal.azure.com).

```XML
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

#### <a name="step-3-lsapplicationqueriesschemes"></a>Stap 3: LSApplicationQueriesSchemes

**Voeg `LSApplicationQueriesSchemes`** toe om het aanroepen van Microsoft Authenticator toe te staan indien geïnstalleerd.
Houd er rekening mee dat het schema msauthv3 vereist is bij het compileren van uw app met Xcode 11 of hoger. 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Brokered-verificatie voor Xamarin. Android

MSAL.NET biedt nog geen ondersteuning voor Brokers voor Android.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token verkrijgen](scenario-mobile-acquire-token.md)
