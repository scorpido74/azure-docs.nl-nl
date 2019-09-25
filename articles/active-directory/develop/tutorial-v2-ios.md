---
title: Aan de slag met iOS en macOS-micro soft Identity platform | Azure
description: Hoe iOS-en macOS-toepassingen (SWIFT) een API kunnen aanroepen waarvoor toegangs tokens zijn vereist met behulp van micro soft Identity platform
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d442508a1ac79a30ba5a02ecd09c27fc0e320ad0
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268300"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Gebruikers aanmelden en de Microsoft Graph aanroepen vanuit een iOS-of macOS-app

In deze zelf studie leert u hoe u een iOS-of macOS-app integreert met het micro soft Identity-platform. De app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API.  

Wanneer u de hand leiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen) en werk-of school accounts van elk bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Hoe deze zelf studie werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

De app in deze zelf studie meldt zich aan bij gebruikers en haalt gegevens op uit hun naam.  Deze gegevens worden geopend via een beveiligde API (Microsoft Graph-API in dit geval) waarvoor autorisatie is vereist en wordt beveiligd door het micro soft Identity-platform.

Met name:

* Uw app meldt zich aan bij de gebruiker via een browser of de Microsoft Authenticator.
* De eind gebruiker accepteert de machtigingen die uw toepassing heeft aangevraagd.
* Uw app krijgt een toegangs token voor de Microsoft Graph-API.
* Het toegangs token wordt opgenomen in de HTTP-aanvraag voor de Web-API.
* Het Microsoft Graph-antwoord verwerken.

In dit voor beeld wordt de micro soft Authentication Library (MSAL) gebruikt om verificatie te implementeren. MSAL zal tokens automatisch vernieuwen, eenmalige aanmelding (SSO) leveren tussen andere apps op het apparaat en de account (s) beheren.

Deze zelf studie is van toepassing op iOS-en macOS-apps. Houd er rekening mee dat sommige stappen verschillen tussen deze twee platforms. 

## <a name="prerequisites"></a>Vereisten

- XCode versie 10. x of hoger is vereist voor het bouwen van de app in deze hand leiding. U kunt XCode downloaden van de [iTunes-website](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode-download-URL").
- Micro soft Authentication Library ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). U kunt een afhankelijkheids Manager gebruiken of de bibliotheek hand matig toevoegen. In de onderstaande instructies ziet u hoe.

In deze zelf studie wordt een nieuw project gemaakt. Als u in plaats daarvan de voltooide zelf studie wilt downloaden, downloadt u de code:
- [iOS-voorbeeld code](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS-voorbeeld code](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Een nieuw project maken

1. Open Xcode en selecteer **een nieuw Xcode-project maken**.
2. Selecteer voor IOS-apps **IOS** > -**app met één weer gave** en selecteer **volgende**.
3. Voor MacOS-apps selecteert u de toepassing **MacOS** > **cacao** en selecteert u **volgende**.
4. Geef een product naam op.
5. Stel de **taal** in op **Swift** en selecteer **volgende**.
6. Selecteer een map voor het maken van uw app en klik op **maken**.

## <a name="register-your-application"></a>Uw toepassing registreren

1. Ga naar [Azure Portal](https://aka.ms/MobileAppReg)
2. Open de [blade app-registraties](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) en klik op **+ nieuwe registratie**.
3. Voer een **naam** in voor uw app en klik vervolgens zonder een omleidings-URI in te stellen op **registreren**.
4. Selecteer in de sectie **beheren** van het deel venster dat wordt weer gegeven de optie **verificatie**.

5. Klik op **de nieuwe ervaring** aan de bovenkant van het scherm om de nieuwe app-registratie te openen en klik vervolgens op **+ nieuwe registratie** >  **+ een platform** > **IOS**toevoegen.
    - Voer de bundel-ID van het project in. Als u de code hebt gedownload, is `com.microsoft.identitysample.MSALiOS`dit. Als u uw eigen project wilt maken, selecteert u uw project in Xcode en opent u het tabblad **Algemeen** . De bundel-id wordt weer gegeven in de sectie **identiteit** .
    - Houd er rekening mee dat u voor macOS ook de iOS-ervaring moet gebruiken. 
6. Klik `Configure` en sla de **MSAL-configuratie** op die wordt weer gegeven op de pagina **IOS-configuratie** zodat u deze kunt invoeren wanneer u de app later configureert.  Klik op **Gereed**.

## <a name="add-msal"></a>MSAL toevoegen

Kies een van de volgende manieren om de MSAL-bibliotheek in uw app te installeren:

### <a name="cocoapods"></a>CocoaPods

1. Als u [CocoaPods](https://cocoapods.org/)gebruikt, moet `MSAL` u eerst een leeg bestand maken dat wordt genoemd `podfile` in dezelfde map als het bestand van `.xcodeproj` uw project. Voeg het volgende toe `podfile`aan:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Vervang `<your-target-here>` door de naam van uw project.
3. Navigeer in een Terminal venster naar de map met het `podfile` bestand dat u hebt gemaakt en voer uit `pod install` om de MSAL-bibliotheek te installeren.
4. Sluit Xcode en open `<your project name>.xcworkspace` om het project in Xcode opnieuw te laden.

### <a name="carthage"></a>Carthage

Als u [Carthage](https://github.com/Carthage/Carthage)gebruikt, installeert `MSAL` u deze aan uw: `Cartfile`

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Voer vanuit een Terminal venster, in dezelfde map als de bijgewerkte `Cartfile`, de volgende opdracht uit om de afhankelijkheden in uw project Carthage bij te werken.

iOS:

```bash
carthage update --platform iOS
```

MacOS

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Handmatig

U kunt ook een git-submodule gebruiken of de meest recente release bekijken om te gebruiken als een framework in uw toepassing.

## <a name="add-your-app-registration"></a>Uw app-registratie toevoegen

Vervolgens voegen we uw app-registratie toe aan uw code. 

Voeg eerst de volgende import instructie toe boven aan de `ViewController.swift` -en `AppDelegate.swift` -bestanden:

```swift
import MSAL
```

Voeg vervolgens de volgende code toe `ViewController.swift` aan `viewDidLoad()`vóór:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParamaters : MSALWebviewParameters?
```

Wijzig de waarde die is `kClientID`toegewezen aan als uw toepassings-id. Deze waarde maakt deel uit van de MSAL-configuratie gegevens die u hebt opgeslagen tijdens de stap aan het begin van deze zelf studie om de toepassing te registreren in de Azure Portal.

## <a name="for-ios-only-configure-url-schemes"></a>Alleen voor iOS: URL-schema's configureren

In deze stap registreert `CFBundleURLSchemes` u zodat de gebruiker na het aanmelden weer naar de app kan worden omgeleid. Op de manier `LSApplicationQueriesSchemes` kan uw app het gebruik van Microsoft Authenticator ook maken.

In Xcode opent `Info.plist` u als een bron code bestand en voegt u het volgende `<dict>` toe aan de sectie. Vervang `[BUNDLE_ID]` door de waarde die u hebt gebruikt in de Azure Portal. Als u de code hebt gedownload `com.microsoft.identitysample.MSALiOS`, is. Als u uw eigen project wilt maken, selecteert u uw project in Xcode en opent u het tabblad **Algemeen** . De bundel-id wordt weer gegeven in de sectie **identiteit** .

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
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Voor alleen macOS, de sandbox voor apps configureren

1. Ga naar het **tabblad** > Xcode-project instellingen > mogelijkheden**app sandbox**
2. Selectie vakje **uitgaande verbindingen (client)** in. 

## <a name="create-your-apps-ui"></a>De gebruikers interface van de app maken

Maak nu een gebruikers interface met een knop voor het aanroepen van de Microsoft Graph-API, een andere om u af te melden en een tekst weergave om een bepaalde uitvoer te `ViewController`bekijken door de volgende code toe te voegen aan de klasse:

### <a name="ios-ui"></a>iOS-gebruikers interface

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>macOS-gebruikers interface

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

Vervang vervolgens de `viewDidLoad()` methode in `ViewController` de-klasse door:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>MSAL gebruiken

### <a name="initialize-msal"></a>MSAL initialiseren

Voeg de volgende `initMSAL` methode toe aan `ViewController` de klasse:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Voeg de volgende After `initMSAL` -methode toe `ViewController` aan de klasse.

### <a name="ios-code"></a>iOS-code:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS-code:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters()
        self.webViewParamaters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Alleen voor iOS: de aanmeldings-call back afhandelen

Open het `AppDelegate.swift`-bestand. Als u de terugroeping wilt afhandelen `MSALPublicClientApplication.handleMSALResponse` na het `appDelegate` aanmelden, voegt u de klasse als volgt toe:

```swift
    // Inside AppDelegate...
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

**Als u Xcode 11 gebruikt**, moet u in plaats daarvan MSAL-Call `SceneDelegate.swift` back plaatsen in de.
Als u zowel UISceneDelegate als UIApplicationDelegate voor compatibiliteit met oudere iOS-versies ondersteunt, moet MSAL call back in beide bestanden worden geplaatst.

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

#### <a name="acquire-tokens"></a>Tokens verkrijgen

Nu kunnen we de UI-verwerkings logica van de toepassing implementeren en tokens interactief ophalen via MSAL.

MSAL beschrijft twee primaire methoden voor het ophalen van tokens `acquireTokenSilently()` : `acquireTokenInteractively()`en: 

- `acquireTokenSilently()`probeert een gebruiker aan te melden en tokens op te halen zonder tussen komst van de gebruiker zolang er een account aanwezig is.

- `acquireTokenInteractively()`de gebruikers interface wordt altijd weer gegeven wanneer u zich probeert aan te melden bij de gebruiker. Het kan gebruikmaken van sessie cookies in de browser of een account in micro soft Authenticator om een interactieve-SSO-ervaring te bieden.

Voeg de volgende code toe aan `ViewController` de klasse:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Een token interactief ophalen

Met de onderstaande code wordt een token voor de eerste keer opgehaald door `MSALInteractiveTokenParameters` het maken van `acquireToken`een object en het aanroepen van. Vervolgens voegt u code toe die:

1. Maakt `MSALInteractiveTokenParameters` met scopes.
2. Aanroepen `acquireToken()` met de gemaakte para meters.
3. Hiermee worden fouten afgehandeld. Raadpleeg de [hand leiding voor het afhandelen van MSAL voor IOS-en macOS-fouten](msal-handling-exceptions.md)voor meer details.
4. Hiermee wordt de geslaagde Case verwerkt.

Voeg de volgende code toe aan `ViewController` de klasse.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParamaters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Een token op de achtergrond ophalen

Als u een bijgewerkt token op de achtergrond wilt verkrijgen, voegt u de `ViewController` volgende code toe aan de klasse. Er worden een `MSALSilentTokenParameters` object en aanroepen `acquireTokenSilent()`gemaakt:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>De Microsoft Graph-API aanroepen 

Zodra u een token hebt, kan uw app dit in de HTTP-header gebruiken om een geautoriseerde aanvraag voor de Microsoft Graph te maken:

| header sleutel    | value                 |
| ------------- | --------------------- |
| Authorization | \<Toegang tot Bearer-token > |

Voeg de volgende code toe aan `ViewController` de klasse:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Zie [Microsoft Graph-API](https://graph.microsoft.com) voor meer informatie over de Microsoft Graph-API.

### <a name="use-msal-for-sign-out"></a>MSAL gebruiken voor afmelden

Voeg vervolgens ondersteuning toe voor afmelden.

> [!Important]
> Als u zich afmeldt bij MSAL, worden alle bekende gegevens van een gebruiker uit de toepassing verwijderd, maar de gebruiker heeft nog steeds een actieve sessie op het apparaat. Als de gebruiker zich opnieuw probeert aan te melden, zien ze mogelijk de gebruikers interface voor aanmelden, maar hoeven ze hun referenties mogelijk niet opnieuw op te geven omdat de sessie van het apparaat nog steeds actief is.

Voeg de volgende code toe in de `ViewController` -klasse om de afmeldings mogelijkheid toe te voegen. Deze methode doorloopt alle accounts en verwijdert deze:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Token cache inschakelen

MSAL slaat standaard de tokens van uw app op in de iOS-of macOS-sleutel hanger. 

Het in cache opslaan van tokens inschakelen:
1. Zorg ervoor dat uw toepassing correct is ondertekend
2. Ga naar het **tabblad** > Xcode-project instellingen > mogelijkheden het**delen van sleutel hanger inschakelen**
3. Klik **+** en voer een vermelding voor de **sleutel keten groep** in: 3. a voor IOS, Voer `com.microsoft.adalcache` 3. b in voor macOS ENTER`com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Hulp methoden toevoegen
Voeg de volgende Help-methoden toe `ViewController` aan de klasse om het voor beeld te volt ooien.

### <a name="ios-ui"></a>iOS-gebruikers interface:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>macOS-gebruikers interface:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Toepassingen met meerdere accounts

Deze app is gebouwd voor een enkel account scenario. MSAL biedt ook ondersteuning voor scenario's met meerdere accounts, maar hiervoor is extra werk van apps vereist. U moet een gebruikers interface maken om gebruikers te helpen bij het selecteren van het account dat ze willen gebruiken voor elke actie die tokens vereist. Uw app kan ook een heuristiek implementeren om te selecteren welk account u wilt gebruiken via de `getAccounts()` -methode.

## <a name="test-your-app"></a>Uw app testen

### <a name="run-locally"></a>Lokaal uitvoeren

Bouw en implementeer de app op een test apparaat of Simulator. U moet zich kunnen aanmelden en tokens ontvangen voor Azure AD of persoonlijke micro soft-accounts.

De eerste keer dat een gebruiker zich bij uw app aanmeldt, wordt de micro soft-identiteit gevraagd om toestemming te geven voor de aangevraagde machtigingen.  Hoewel de meeste gebruikers kunnen worden gemachtigd, hebben sommige Azure AD-tenants de toestemming van de gebruiker uitgeschakeld. hiervoor moeten beheerders toestemming geven namens alle gebruikers. Als u dit scenario wilt ondersteunen, moet u de scopes van uw app registreren in de Azure Portal.

Nadat u zich hebt aangemeld, worden in de app de gegevens weer gegeven die `/me` door het Microsoft Graph-eind punt zijn geretourneerd.

## <a name="get-help"></a>Help opvragen

Ga naar [Help en ondersteuning](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) als u problemen ondervindt met deze zelf studie of met het micro soft Identity-platform.

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
