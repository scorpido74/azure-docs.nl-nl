---
title: Mobiele apps configureren die web-API's aanroepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-API's aanroept (de codeconfiguratie van de app)
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
ms.openlocfilehash: fc25f13d0b0b8a264dcd47a5fdebb0533e93fb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132488"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Een mobiele app configureren die web-API's aanroept

Nadat u uw toepassing hebt gemaakt, leert u hoe u de code configureren met behulp van de parameters voor app-registratie. Mobiele toepassingen presenteren een aantal complexiteiten in verband met de aanpassing in hun creatie kader.

## <a name="find-msal-support-for-mobile-apps"></a>MSAL-ondersteuning zoeken voor mobiele apps

De volgende MSAL-typen (Microsoft Authentication Library) ondersteunen mobiele apps.

MSAL | Beschrijving
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Gebruikt om draagbare toepassingen te ontwikkelen. MSAL.NET ondersteunt de volgende platforms voor het bouwen van een mobiele applicatie: Universal Windows Platform (UWP), Xamarin.iOS en Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Wordt gebruikt om native iOS-toepassingen te ontwikkelen met Behulp van Objective-C of Swift.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Wordt gebruikt om native Android-applicaties te ontwikkelen in Java voor Android.

## <a name="instantiate-the-application"></a>Instantiate de toepassing

### <a name="android"></a>Android

Mobiele applicaties `PublicClientApplication` maken gebruik van de klasse. Zo u het momentaniate:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobiele applicaties op iOS moeten de `MSALPublicClientApplication` klasse instantiëren. Als u de klasse wilt instantiëren, gebruikt u de volgende code. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Aanvullende MSALPublicClientApplicationConfig-eigenschappen](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) kunnen de standaardautoriteit overschrijven, een omleidingsURI opgeven of het gedrag van MSAL-tokencaching wijzigen. 

### <a name="xamarin-or-uwp"></a>Xamarin of UWP

In dit gedeelte wordt uitgelegd hoe u de toepassing voor Xamarin.iOS-, Xamarin.Android- en UWP-apps instantiëren.

#### <a name="instantiate-the-application"></a>Instantiate de toepassing

In Xamarin of UWP is de eenvoudigste manier om de toepassing te instantiëren de volgende code. In deze `ClientId` code is de GUID van uw geregistreerde app.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Aanvullende `With<Parameter>` methoden stellen de ui-ouder in, overschrijven de standaardautoriteit, geven een clientnaam en versie op voor telemetrie, geven een omleidingsURI op en geven de HTTP-fabriek op die u wilt gebruiken. De HTTP-fabriek kan bijvoorbeeld worden gebruikt om proxy's te verwerken en telemetrie en logboekregistratie op te geven. 

In de volgende secties vindt u meer informatie over het momentmaken van de toepassing.

##### <a name="specify-the-parent-ui-window-or-activity"></a>De bovenliggende gebruikersinterface, venster of activiteit opgeven

Op Android moet u de bovenliggende activiteit doorgeven voordat u de interactieve verificatie doet. Op iOS, wanneer u een makelaar gebruikt, `ViewController`moet u doorgeven. Op dezelfde manier op UWP wilt u misschien het bovenliggende venster doorgeven. Je geeft het door wanneer je het token verwerft. Maar wanneer u de app maakt, u ook een `UIParent`terugroepopgeven als gemachtigde die terugkeert.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Op Android raden we [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)je aan om . De resulterende `PublicClientApplication` bouwercode ziet er als volgt uit:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Meer parameters voor app-building zoeken

Zie de lijst Methoden voor een `PublicClientApplicationBuilder`lijst met alle methoden die beschikbaar zijn op , voor [een](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)lijst met alle methoden die beschikbaar zijn .

Zie de referentiedocumentatie voor een `PublicClientApplicationOptions`beschrijving van alle opties die in de [referentiedocumentatie](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)worden weergegeven .

## <a name="tasks-for-xamarin-ios"></a>Taken voor Xamarin iOS

Als u MSAL.NET op Xamarin iOS gebruikt, verricht u de volgende taken.

* [De `OpenUrl` functie overschrijven en implementeren in`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Sleutelhangergroepen inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Delen van tokencache inschakelen](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Toegang tot sleutelhanger inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Zie [Xamarin iOS-overwegingen](msal-net-xamarin-ios-considerations.md)voor meer informatie.

## <a name="tasks-for-msal-for-ios-and-macos"></a>Taken voor MSAL voor iOS en macOS

Deze taken zijn nodig wanneer u MSAL voor iOS en macOS gebruikt:

* [De `openURL` callback implementeren](#brokered-authentication-for-msal-for-ios-and-macos)
* [Toegangsgroepen voor sleutelhangers inschakelen](howto-v2-keychain-objc.md)
* [Browsers en WebView aanpassen](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Taken voor Xamarin.Android

Als u Xamarin.Android gebruikt, gaat u de volgende taken uitvoeren:

- [Zorg ervoor dat de controle teruggaat naar MSAL nadat het interactieve gedeelte van de verificatiestroom is beëindigd](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Update het Android-manifest](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [De ingesloten webweergave gebruiken (optioneel)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Problemen oplossen indien nodig](msal-net-xamarin-android-considerations.md#troubleshoot)

Zie [Overwegingen Xamarin.Android voor](msal-net-xamarin-android-considerations.md)meer informatie.

Voor overwegingen over de browsers op Android, zie [Xamarin.Android-specifieke overwegingen met MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Taken voor UWP

Op UWP u bedrijfsnetwerken gebruiken. In de volgende secties worden de taken uitgelegd die u moet uitvoeren in het bedrijfsscenario.

Zie [UWP-specifieke overwegingen met MSAL.NET](msal-net-uwp-considerations.md)voor meer informatie.

## <a name="configure-the-application-to-use-the-broker"></a>De toepassing configureren om de broker te gebruiken

Op Android en iOS schakelen brokers in:

- **Enkele aanmelding (SSO)**: U SSO gebruiken voor apparaten die zijn geregistreerd bij Azure Active Directory (Azure AD). Wanneer u SSO gebruikt, hoeven uw gebruikers zich niet bij elke toepassing aan te melden.
- **Apparaatidentificatie:** met deze instelling u beleid voor voorwaardelijke toegang instellen dat is gerelateerd aan Azure AD-apparaten. Het verificatieproces maakt gebruik van het apparaatcertificaat dat is gemaakt toen het apparaat is verbonden met de werkplek.
- **Verificatie van toepassingsidentificatie:** Wanneer een toepassing de makelaar aanroept, passeert deze de omleidings-URL. Dan verifieert de makelaar het.

### <a name="enable-the-broker-on-xamarin"></a>Schakel de makelaar in op Xamarin

Als u de broker op Xamarin wilt inschakelen, gebruikt u de `WithBroker()` parameter wanneer u de `PublicClientApplicationBuilder.CreateApplication` methode aanroept. Standaard `.WithBroker()` is ingesteld op true. 

Volg de stappen in de [sectie Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) in dit artikel om gebrokereerde verificatie voor Xamarin.iOS in te schakelen.

### <a name="enable-the-broker-for-msal-for-android"></a>Schakel de broker voor MSAL voor Android in

Zie [Brokered authentication op Android](brokered-auth.md)voor informatie over het inschakelen van een broker op Android. 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Schakel de broker voor MSAL in voor iOS en macOS

Brokered authentication is standaard ingeschakeld voor Azure AD-scenario's in MSAL voor iOS en macOS. 

In de volgende secties worden instructies gegeven voor het configureren van uw toepassing voor ondersteuning voor gebrokereerde verificatie voor MSAL voor Xamarin.iOS of MSAL voor iOS en macOS. In de twee sets van instructies, sommige van de stappen verschillen.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Brokered authentication inschakelen voor Xamarin iOS

Volg de stappen in deze sectie om uw Xamarin.iOS-app in staat te stellen te praten met de [Microsoft Authenticator-app.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Stap 1: Ondersteuning voor makelaars inschakelen

Ondersteuning voor broker is standaard uitgeschakeld. U schakelt het `PublicClientApplication` in voor een individuele klasse. Gebruik `WithBroker()` de parameter wanneer `PublicClientApplication` u `PublicClientApplicationBuilder`de klasse maakt via . De `WithBroker()` parameter is standaard ingesteld op true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Stap 2: AppDelegate bijwerken om de terugbelfunctie af te handelen

Wanneer MSAL.NET de makelaar belt, belt de makelaar terug naar uw aanvraag. Het roept terug `AppDelegate.OpenUrl` met behulp van de methode. Omdat MSAL wacht op het antwoord van de makelaar, moet uw aanvraag samenwerken om MSAL.NET terug te bellen. U stelt dit gedrag `AppDelegate.cs` in door het bestand bij te werken om de methode te overschrijven, zoals de volgende code laat zien.

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

Deze methode wordt elke keer dat de toepassing wordt gestart aangeroepen. Het is een kans om de reactie van de makelaar te verwerken en het verificatieproces te voltooien dat MSAL.NET gestart.

#### <a name="step-3-set-a-uiviewcontroller"></a>Stap 3: Een UIViewController instellen()

Voor Xamarin iOS hoeft u normaal gesproken geen objectvenster in te stellen. Maar in dit geval moet u instellen, zodat u verzenden en ontvangen van reacties van een makelaar. Als u een objectvenster wilt instellen, stelt `AppDelegate.cs`u een `ViewController`.

Voer de volgende stappen uit om het objectvenster in te stellen:

1. In `AppDelegate.cs`, `App.RootViewController` zet de `UIViewController()`op een nieuwe . Deze instelling zorgt ervoor dat `UIViewController`de oproep naar de makelaar omvat . Als deze niet correct is ingesteld, krijgt u mogelijk de fout:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Gebruik `AcquireTokenInteractive` tijdens het `.WithParentActivityOrWindow(App.RootViewController)`gesprek. Geef de verwijzing door naar het objectvenster dat u zult gebruiken. Hier volgt een voorbeeld:

    In `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    In `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    In `AcquireToken` de oproep:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>Stap 4: Een URL-schema registreren

MSAL.NET gebruikt URL's om de makelaar aan te roepen en de broker-reactie terug te sturen naar uw app. Als u de heen- en terugreis `Info.plist` wilt voltooien, registreert u het URL-schema van uw app in het bestand. 

Voer de volgende stappen uit om het URL-schema van uw app te registreren:

1. `CFBundleURLSchemes` Voorvoegsel `msauth`met . 
1. Voeg `CFBundleURLName` toe aan het einde. Volg dit patroon: 

   `$"msauth.(BundleId)"`

   Hier `BundleId` identificeert u uw apparaat op unieke wijze. Bijvoorbeeld, als `BundleId` `yourcompany.xforms`is, uw `msauth.com.yourcompany.xforms`URL-schema is .
    
   > [!NOTE]
   > Dit URL-schema wordt onderdeel van de omleiding URI die uw app op unieke wijze identificeert wanneer deze het antwoord van de makelaar ontvangt.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Stap 5: Toevoegen aan de sectie LSApplicationQueriesSchemes

MSAL `–canOpenURL:` gebruikt om te controleren of de makelaar is geïnstalleerd op het apparaat. In iOS 9 heeft Apple de schema's vergrendeld waarvoor een toepassing kan zoeken.

Toevoegen `msauthv2` aan `LSApplicationQueriesSchemes` het `Info.plist` gedeelte van het bestand, zoals in het volgende codevoorbeeld:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Brokered authentication voor MSAL voor iOS en macOS

Brokered authentication is standaard ingeschakeld voor Azure AD-scenario's.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Stap 1: AppDelegate bijwerken om de terugbelfunctie af te handelen

Wanneer MSAL voor iOS en macOS de makelaar belt, belt `openURL` de makelaar terug naar uw toepassing met behulp van de methode. Omdat MSAL wacht op het antwoord van de makelaar, moet uw aanvraag samenwerken om MSAL terug te bellen. Stel deze mogelijkheid in `AppDelegate.m` door het bestand bij te werken om de methode te overschrijven, zoals de volgende codevoorbeelden laten zien.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Als u `UISceneDelegate` hebt aangenomen op iOS 13 of hoger, `scene:openURLContexts:` `UISceneDelegate` plaats dan de MSAL callback in de plaats. MSAL `handleMSALResponse:sourceApplication:` mag slechts één keer worden aangeroepen voor elke URL.
>
> Zie voor meer informatie de [Apple-documentatie](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Stap 2: Een URL-schema registreren

MSAL voor iOS en macOS gebruikt URL's om de makelaar aan te roepen en vervolgens de broker-reactie terug te sturen naar uw app. Als u de heen- en terugreis `Info.plist` wilt voltooien, registreert u een URL-schema voor uw app in het bestand.

Ga als lid van uw app naar een schema: 

1. Voorvoeg uw aangepaste `msauth`URL-schema vooraf met . 

1. Voeg uw bundel-id toe aan het einde van uw schema. Volg dit patroon: 

   `$"msauth.(BundleId)"`

   Hier `BundleId` identificeert u uw apparaat op unieke wijze. Bijvoorbeeld, als `BundleId` `yourcompany.xforms`is, uw `msauth.com.yourcompany.xforms`URL-schema is .
  
   > [!NOTE]
   > Dit URL-schema wordt onderdeel van de omleiding URI die uw app op unieke wijze identificeert wanneer deze het antwoord van de makelaar ontvangt. Controleer of de omleidingsURI `msauth.(BundleId)://auth` in de indeling is geregistreerd voor uw toepassing in de [Azure-portal.](https://portal.azure.com)
  
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Stap 3: LSApplicationQueriesSchemes toevoegen

Toevoegen `LSApplicationQueriesSchemes` om oproepen naar de Microsoft Authenticator-app toe te staan als deze is geïnstalleerd.

> [!NOTE]
> Het `msauthv3` schema is nodig wanneer uw app wordt samengesteld met Xcode 11 en hoger. 

Hier is een voorbeeld van `LSApplicationQueriesSchemes`hoe toe te voegen:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Gebrokereerde verificatie voor Xamarin.Android

MSAL.NET ondersteunt geen brokers voor Android.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een token aanschaffen](scenario-mobile-acquire-token.md)
