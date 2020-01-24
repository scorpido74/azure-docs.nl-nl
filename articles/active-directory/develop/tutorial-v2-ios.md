---
title: MSAL voor iOS & macOS-zelf studie-micro soft Identity platform | Azure
description: Meer informatie over hoe iOS-en macOS-apps (SWIFT) een API kunnen aanroepen waarvoor toegangs tokens zijn vereist met het micro soft Identity-platform
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
ms.openlocfilehash: a3a1b424cdf3c1897efce36b4499de967f85bb1e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701209"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Gebruikers aanmelden en de Microsoft Graph aanroepen vanuit een iOS-of macOS-app

In deze zelf studie leert u hoe u een iOS-of macOS-app integreert met het micro soft Identity-platform. De app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API.  

Wanneer u de hand leiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen) en werk-of school accounts van elk bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Hoe deze zelf studie werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

De app in deze zelf studie meldt zich aan bij gebruikers en haalt gegevens op uit hun naam.  Deze gegevens worden geopend via een beveiligde API (Microsoft Graph-API in dit geval) waarvoor autorisatie is vereist en wordt beveiligd door het micro soft Identity-platform.

Meer details:

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
2. Voor iOS-apps selecteert u **ios** > **app met één weer gave** en selecteert u **volgende**.
3. Selecteer voor macOS-apps **macos** > **cacao-app** en selecteer **volgende**.
4. Geef een product naam op.
5. Stel de **taal** in op **Swift** en selecteer **volgende**.
6. Selecteer een map voor het maken van uw app en klik op **maken**.

## <a name="register-your-application"></a>Uw toepassing registreren

1. Ga naar [Azure Portal](https://aka.ms/MobileAppReg)
2. Open de [blade app-registraties](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) en klik op **+ nieuwe registratie**.
3. Voer een **naam** in voor uw app en klik vervolgens zonder een omleidings-URI in te stellen op **registreren**.
4. Selecteer in de sectie **beheren** van het deel venster dat wordt weer gegeven de optie **verificatie**.

5. Klik op **de nieuwe ervaring** boven aan het scherm proberen om de nieuwe app-registratie te openen en klik vervolgens op **+ nieuwe registratie** >  **+ een platform toe te voegen** > **IOS**.
    - Voer de bundel-ID van het project in. Als u de code hebt gedownload, is dit `com.microsoft.identitysample.MSALiOS`. Als u uw eigen project wilt maken, selecteert u uw project in Xcode en opent u het tabblad **Algemeen** . De bundel-id wordt weer gegeven in de sectie **identiteit** .
    - Houd er rekening mee dat u voor macOS ook de iOS-ervaring moet gebruiken. 
6. Klik op `Configure` en sla de **MSAL-configuratie** op die wordt weer gegeven op de pagina IOS- **configuratie** zodat u deze kunt invoeren wanneer u de app later configureert.  Klik op **Gereed**.

## <a name="add-msal"></a>MSAL toevoegen

Kies een van de volgende manieren om de MSAL-bibliotheek in uw app te installeren:

### <a name="cocoapods"></a>CocoaPods

1. Als u [CocoaPods](https://cocoapods.org/)gebruikt, installeert u `MSAL` door eerst een leeg bestand met de naam `podfile` te maken in dezelfde map als het `.xcodeproj`-bestand van het project. Voeg het volgende toe aan `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Vervang `<your-target-here>` door de naam van uw project.
3. Navigeer in een Terminal venster naar de map die de `podfile` bevat die u hebt gemaakt en voer `pod install` uit om de MSAL-bibliotheek te installeren.
4. Sluit Xcode en open `<your project name>.xcworkspace` om het project in Xcode opnieuw te laden.

### <a name="carthage"></a>Carthage

Als u [Carthage](https://github.com/Carthage/Carthage)gebruikt, installeert u `MSAL` door het toe te voegen aan uw `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Voer vanuit een Terminal venster, in dezelfde map als de bijgewerkte `Cartfile`, de volgende opdracht uit om de afhankelijkheden in uw project Carthage bij te werken.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Handmatig

U kunt ook een git-submodule gebruiken of de meest recente release bekijken om te gebruiken als een framework in uw toepassing.

## <a name="add-your-app-registration"></a>Uw app-registratie toevoegen

Vervolgens voegen we uw app-registratie toe aan uw code. 

Voeg eerst de volgende import instructie toe aan de bovenkant van de `ViewController.swift`-en `AppDelegate.swift`-bestanden:

```swift
import MSAL
```

Voeg vervolgens de volgende code toe aan `ViewController.swift` voorafgaand aan `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

De enige waarde die u hierboven moet wijzigen, is de waarde die is toegewezen aan `kClientID`om uw [toepassings-id](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id)te zijn. Deze waarde maakt deel uit van de MSAL-configuratie gegevens die u hebt opgeslagen tijdens de stap aan het begin van deze zelf studie om de toepassing te registreren in de Azure Portal.

## <a name="for-ios-only-configure-url-schemes"></a>Alleen voor iOS: URL-schema's configureren

In deze stap registreert u `CFBundleURLSchemes`, zodat de gebruiker opnieuw kan worden omgeleid naar de App nadat u zich hebt aangemeld. Op de manier kan `LSApplicationQueriesSchemes` ook het gebruik van Microsoft Authenticator maken met uw app.

Open `Info.plist` als een bron code bestand in Xcode en voeg het volgende toe in de sectie `<dict>`. Vervang `[BUNDLE_ID]` door de waarde die u hebt gebruikt in het Azure Portal dat, als u de code hebt gedownload, `com.microsoft.identitysample.MSALiOS`. Als u uw eigen project wilt maken, selecteert u uw project in Xcode en opent u het tabblad **Algemeen** . De bundel-id wordt weer gegeven in de sectie **identiteit** .

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

1. Ga naar het tabblad Xcode-project instellingen > **mogelijkheden** > **sandbox voor apps**
2. Selectie vakje **uitgaande verbindingen (client)** in. 

## <a name="create-your-apps-ui"></a>De gebruikers interface van de app maken

Maak nu een gebruikers interface met een knop voor het aanroepen van de Microsoft Graph-API, een andere om u af te melden en een tekst weergave om een bepaalde uitvoer te zien door de volgende code toe te voegen aan de `ViewController`-klasse:

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

Vervang vervolgens in de `ViewController`-klasse de methode `viewDidLoad()` door:

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

Voeg de volgende `initMSAL` methode toe aan de klasse `ViewController`:

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

Voeg de volgende na `initMSAL`-methode toe aan de `ViewController`-klasse.

### <a name="ios-code"></a>iOS-code:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS-code:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Alleen voor iOS: de aanmeldings-call back afhandelen

Open het `AppDelegate.swift`-bestand. Als u de terugroeping wilt afhandelen na het aanmelden, voegt u `MSALPublicClientApplication.handleMSALResponse` als volgt toe aan de `appDelegate` klasse:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Als u Xcode 11 gebruikt**, moet u in plaats daarvan MSAL-call back in de `SceneDelegate.swift` plaatsen.
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

MSAL beschrijft twee primaire methoden voor het ophalen van tokens: `acquireTokenSilently()` en `acquireTokenInteractively()`: 

- `acquireTokenSilently()` probeert zich aan te melden bij een gebruiker en tokens op te halen zonder tussen komst van de gebruiker zolang er een account aanwezig is.

- in `acquireTokenInteractively()` wordt de gebruikers interface altijd weer gegeven wanneer u zich probeert aan te melden bij de gebruiker. Het kan gebruikmaken van sessie cookies in de browser of een account in micro soft Authenticator om een interactieve-SSO-ervaring te bieden.

Voeg de volgende code toe aan de klasse `ViewController`:

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

Met de onderstaande code wordt een token voor de eerste keer opgehaald door het maken van een `MSALInteractiveTokenParameters`-object en het aanroepen van `acquireToken`. Vervolgens voegt u code toe die:

1. Hiermee maakt u `MSALInteractiveTokenParameters` met scopes.
2. Roept `acquireToken()` aan met de gemaakte para meters.
3. Hiermee worden fouten afgehandeld. Raadpleeg de [hand leiding voor het afhandelen van MSAL voor IOS-en macOS-fouten](msal-handling-exceptions.md)voor meer details.
4. Hiermee wordt de geslaagde Case verwerkt.

Voeg de volgende code toe aan de klasse `ViewController`.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
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

Als u een bijgewerkt token op de achtergrond wilt verkrijgen, voegt u de volgende code toe aan de klasse `ViewController`. Hiermee maakt u een `MSALSilentTokenParameters`-object en roept u `acquireTokenSilent()`op:

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

| header sleutel    | waarde                 |
| ------------- | --------------------- |
| Autorisatie | Toegang tot \<-token van Bearer-> |

Voeg de volgende code toe aan de klasse `ViewController`:

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

Voeg de volgende code toe aan de klasse `ViewController` om de afmeldings functie toe te voegen. Deze methode doorloopt alle accounts en verwijdert deze:

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
2. Ga naar het tabblad Xcode-project instellingen > **mogelijkheden** > het **delen van sleutel hanger inschakelen**
3. Klik op **+** en voer een van de volgende **sleutel keten groepen** in: 3. a voor IOS, voer `com.microsoft.adalcache` 3. b in voor macOS Enter `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Hulp methoden toevoegen
Voeg de volgende Help-methoden toe aan de klasse `ViewController` om het voor beeld te volt ooien.

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

Deze app is gebouwd voor een enkel account scenario. MSAL biedt ook ondersteuning voor scenario's met meerdere accounts, maar hiervoor is extra werk van apps vereist. U moet een gebruikers interface maken om gebruikers te helpen bij het selecteren van het account dat ze willen gebruiken voor elke actie die tokens vereist. Uw app kan ook een heuristiek implementeren om te selecteren welk account u wilt gebruiken via de `getAccounts()` methode.

## <a name="test-your-app"></a>Uw app testen

### <a name="run-locally"></a>Lokaal uitvoeren

Bouw en implementeer de app op een test apparaat of Simulator. U moet zich kunnen aanmelden en tokens ontvangen voor Azure AD of persoonlijke micro soft-accounts.

De eerste keer dat een gebruiker zich bij uw app aanmeldt, wordt de micro soft-identiteit gevraagd om toestemming te geven voor de aangevraagde machtigingen.  Hoewel de meeste gebruikers kunnen worden gemachtigd, hebben sommige Azure AD-tenants de toestemming van de gebruiker uitgeschakeld. hiervoor moeten beheerders toestemming geven namens alle gebruikers. Als u dit scenario wilt ondersteunen, moet u de scopes van uw app registreren in de Azure Portal.

Nadat u zich hebt aangemeld, worden in de app de gegevens weer gegeven die door de Microsoft Graph `/me` eind punt zijn geretourneerd.

## <a name="get-help"></a>Hulp krijgen

Ga naar [Help en ondersteuning](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) als u problemen ondervindt met deze zelf studie of met het micro soft Identity-platform.

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
