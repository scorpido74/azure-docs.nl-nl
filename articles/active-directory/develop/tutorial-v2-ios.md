---
title: MSAL voor iOS & macOS-zelfstudie - Microsoft-identiteitsplatform | Azure
description: Ontdek hoe iOS- en macOS-apps (Swift) een API kunnen aanroepen waarvoor toegangstokens nodig zijn met behulp van het Microsoft-identiteitsplatform
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3569d0404f6c0c7f78344ba53733ba2c0c43d979
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880802"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Gebruikers aanmelden en de Microsoft Graph bellen vanuit een iOS- of macOS-app

In deze zelfstudie leert u hoe u een iOS- of macOS-app integreren met het Microsoft-identiteitsplatform. De app meldt zich aan bij een gebruiker, krijgt een toegangstoken om de Microsoft Graph-API aan te roepen en doet een verzoek voor de Microsoft Graph API.  

Wanneer u de handleiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke Microsoft-accounts (waaronder outlook.com, live.com en anderen) en werk- of schoolaccounts van bedrijven of organisaties die Azure Active Directory gebruiken.

>[!NOTE]
> Als u nieuw bent op het Microsoft-identiteitsplatform, raden we u aan om te beginnen met de [aanmeldende gebruikers en de Microsoft Graph API te bellen vanuit een iOS- of macOS-app.](quickstart-v2-ios.md)

## <a name="how-this-tutorial-works"></a>Hoe deze zelfstudie werkt

![Laat zien hoe de voorbeeld-app die door deze zelfstudie wordt gegenereerd, werkt](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

De app in deze zelfstudie zal gebruikers aanmelden en namens hen gegevens krijgen.  Deze gegevens worden geopend via een beveiligde API (Microsoft Graph API in dit geval) waarvoor autorisatie vereist is en wordt beschermd door het Microsoft-identiteitsplatform.

Met name:

* Uw app meldt zich aan bij de gebruiker via een browser of de Microsoft Authenticator.
* De eindgebruiker accepteert de machtigingen die uw aanvraag heeft aangevraagd.
* Uw app krijgt een toegangstoken voor de Microsoft Graph-API.
* Het toegangstoken wordt opgenomen in het HTTP-verzoek tot de web-API.
* De Reactie van Microsoft Graph verwerken.

In dit voorbeeld wordt de Microsoft Authentication-bibliotheek (MSAL) gebruikt om Verificatie te implementeren. MSAL verlengt automatisch tokens, levert single sign-on (SSO) tussen andere apps op het apparaat en beheert het account(s).

Deze zelfstudie is van toepassing op zowel iOS- als macOS-apps. Houd er rekening mee dat sommige stappen verschillen tussen deze twee platforms. 

## <a name="prerequisites"></a>Vereisten

- XCode versie 11.x of meer is vereist om de app te bouwen in deze handleiding. U XCode downloaden van de [iTunes-website.](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode-download-URL")
- Microsoft Authentication Library[(MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). U een afhankelijkheidsmanager gebruiken of de bibliotheek handmatig toevoegen. De onderstaande instructies laten zien hoe.

Deze zelfstudie maakt een nieuw project. Als u in plaats daarvan de voltooide zelfstudie wilt downloaden, downloadt u de code:
- [iOS-voorbeeldcode](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS-voorbeeldcode](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Een nieuw project maken

1. Open Xcode en selecteer **Een nieuw Xcode-project maken**.
2. Selecteer voor iOS-apps **de optie iOS** > **Single view App** en selecteer **Volgende**.
3. Selecteer voor macOS-apps **macOS** > **Cocoa App** en selecteer **Volgende**.
4. Geef een productnaam op.
5. Stel de **taal** in op **Swift** en selecteer **Volgende**.
6. Selecteer een map om uw app te maken en klik op **Maken**.

## <a name="register-your-application"></a>Uw toepassing registreren

1. Ga naar [Azure Portal](https://aka.ms/MobileAppReg)
2. Open het mes app-registraties en klik op **+Nieuwe registratie**.
3. Voer een **naam** in voor uw app en zonder een OMLEIDINGsURI in te stellen.
4. Selecteer **Accounts in een organisatiemap (Azure AD-map - Multitenant) en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox)** onder Ondersteunde **accounttypen**
5. Klik **op Registreren**
6. Selecteer **Verificatie**in het gedeelte **Beheren** van het deelvenster dat wordt weergegeven .

7. Klik **op Probeer de nieuwe ervaring** boven aan het scherm uit om de nieuwe ervaring voor app-registratie te openen en klik vervolgens op **+Nieuwe registratie** > + Een platform > **toevoegen****iOS/macOS**.
    - Voer de bundel-id van uw project in. Als je de code hebt `com.microsoft.identitysample.MSALiOS`gedownload, is dit . Als u uw eigen project maakt, selecteert u uw project in Xcode en opent u het tabblad **Algemeen.** De bundel-id wordt weergegeven in de sectie **Identiteit.**
8. Klik `Configure` op de **MSAL-configuratie** die op de **MSAL-configuratiepagina** wordt weergegeven en sla deze op, zodat u deze invoeren wanneer u uw app later configureert.  Klik op **Gereed**.

## <a name="add-msal"></a>MSAL toevoegen

Kies een van de volgende manieren om de MSAL-bibliotheek in uw app te installeren:

### <a name="cocoapods"></a>Cacaopeulen

1. Als u [CocoaPods](https://cocoapods.org/)gebruikt, `MSAL` installeert u door `podfile` eerst een leeg bestand `.xcodeproj` te maken dat in dezelfde map wordt aangeroepen als het bestand van uw project. Voeg het `podfile`volgende toe aan:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Vervang `<your-target-here>` door de naam van uw project.
3. Navigeer in een terminalvenster naar de `podfile` map met `pod install` de map die u hebt gemaakt en voer deze uit om de MSAL-bibliotheek te installeren.
4. Sluit Xcode `<your project name>.xcworkspace` en open om het project opnieuw te laden in Xcode.

### <a name="carthage"></a>Carthago

Als u [Carthago](https://github.com/Carthage/Carthage)gebruikt, `MSAL` installeer het `Cartfile`door het toe te voegen aan uw:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Voer vanuit een terminalvenster in dezelfde `Cartfile`map als de bijgewerkte map de volgende opdracht uit om Carthago de afhankelijkheden in uw project te laten bijwerken.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Handmatig

U ook Git Submodule gebruiken, of de nieuwste release bekijken om te gebruiken als kader in uw toepassing.

## <a name="add-your-app-registration"></a>Uw app-registratie toevoegen

Vervolgens voegen we uw app-registratie toe aan uw code. 

Voeg eerst de volgende importinstructie toe `ViewController.swift`aan de `AppDelegate.swift` `SceneDelegate.swift` bovenkant van de , evenals of bestanden:

```swift
import MSAL
```

Voeg vervolgens de `ViewController.swift` volgende `viewDidLoad()`code toe aan:

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
    
let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user
    
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

De enige waarde die u hierboven moet `kClientID`wijzigen, is de waarde die is toegewezen aan uw [toepassings-id.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id) Deze waarde maakt deel uit van de MSAL-configuratiegegevens die u tijdens de stap aan het begin van deze zelfstudie hebt opgeslagen om de toepassing in de Azure-portal te registreren.

## <a name="for-ios-only-configure-url-schemes"></a>Alleen voor iOS URL-schema's configureren

In deze stap registreert `CFBundleURLSchemes` u zich zodat de gebruiker na het aanmelden kan worden doorgestuurd naar de app. Overigens kan `LSApplicationQueriesSchemes` uw app ook gebruik maken van Microsoft Authenticator.

Open in `Info.plist` Xcode als broncodebestand en voeg de `<dict>` volgende binnenkant van de sectie toe. Vervangen `[BUNDLE_ID]` door de waarde die u hebt gebruikt in de `com.microsoft.identitysample.MSALiOS`Azure-portal die, als u de code hebt gedownload, . Als u uw eigen project maakt, selecteert u uw project in Xcode en opent u het tabblad **Algemeen.** De bundel-id wordt weergegeven in de sectie **Identiteit.**

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

## <a name="for-macos-only-configure-app-sandbox"></a>Alleen voor macOS configureren App Sandbox

1. Ga naar uw Xcode Project Instellingen > **App** > **Sandbox**
2. Schakel het selectievakje **Uitgaande verbindingen (client)** in. 

## <a name="create-your-apps-ui"></a>De gebruikersinterface van uw app maken

Maak nu een gebruikersinterface met een knop om de Microsoft Graph API aan te roepen, een andere `ViewController`om u af te melden en een tekstweergave om een uitvoer te zien door de volgende code aan de klasse toe te voegen:

### <a name="ios-ui"></a>iOS-gebruikersinterface

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {
        
    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right
        
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
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
        
    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)
        
    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false
        
    self.view.addSubview(loggingText)
        
    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {
                
    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)
        
}
    
@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS-gebruikersinterface

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!
    
var usernameLabel: NSTextField!

func initUI() {
        
    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
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

func platformViewDidLoadSetup() {}

```

Vervang vervolgens ook `ViewController` binnen de `viewDidLoad()` klasse de methode door:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()
        
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
        
        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>MSAL gebruiken

### <a name="initialize-msal"></a>MSAL initialiseren

Voeg de `initMSAL` volgende `ViewController` methode toe aan de klasse:

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

Voeg de `initMSAL` volgende methode `ViewController` na de les toe.

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
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Alleen voor iOS de aanmeldingsterugroep verwerken

Open het `AppDelegate.swift`-bestand. Als u de terugbelna aanmelding wilt afhandelen, voegt u als volgt toe aan `MSALPublicClientApplication.handleMSALResponse` de `appDelegate` klasse:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Als u Xcode 11 gebruikt,** moet u MSAL callback in plaats `SceneDelegate.swift` daarvan plaatsen.
Als u zowel UISceneDelegate als UIApplicationDelegate ondersteunt voor compatibiliteit met oudere iOS, moet MSAL-terugroepen in beide bestanden worden geplaatst.

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

#### <a name="acquire-tokens"></a>Tokens verwerven

Nu kunnen we de ui-verwerkingslogica van de toepassing implementeren en tokens interactief ontvangen via MSAL.

MSAL onthult twee primaire methoden `acquireTokenSilently()` voor `acquireTokenInteractively()`het verkrijgen van tokens: en: 

- `acquireTokenSilently()`probeert een gebruiker aan te melden en tokens te krijgen zonder enige interactie van de gebruiker, zolang er een account aanwezig is. `acquireTokenSilently()`vereist het `MSALAccount` verstrekken van een geldige die kan worden opgehaald met behulp van een van MSAL-account opsomming API's. In dit `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` voorbeeld wordt de lopende rekening opgehaald. 

- `acquireTokenInteractively()`toont altijd de gebruikersinterface wanneer u probeert zich aan te melden bij de gebruiker. Het kan sessiecookies in de browser of een account in de Microsoft-authenticator gebruiken om een interactieve SSO-ervaring te bieden.

Voeg de volgende `ViewController` code toe aan de klasse:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {
        
        self.loadCurrentAccount { (account) in
            
            guard let currentAccount = account else {
                
                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }
            
            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main
                
        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in
            
            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }
            
            if let currentAccount = currentAccount {
                
                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")
                
                self.updateCurrentAccount(account: currentAccount)
                
                if let completion = completion {
                    completion(self.currentAccount)
                }
                
                return
            }
            
            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)
            
            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Een token interactief ophalen

De onderstaande code krijgt voor het `MSALInteractiveTokenParameters` eerst een `acquireToken`token door een object te maken en aan te roepen. Vervolgens voegt u code toe die:

1. Maakt `MSALInteractiveTokenParameters` met scopes.
2. Oproepen `acquireToken()` met de gemaakte parameters.
3. Verwerkt fouten. Voor meer details verwijzen we je door naar de [MSAL voor iOS- en macOS-foutafhandelingshandleiding.](msal-handling-exceptions.md)
4. Behandelt de succesvolle zaak.

Voeg de volgende `ViewController` code toe aan de klasse.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount
        
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
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Een token in stilte krijgen

Als u een bijgewerkt token in stilte `ViewController` wilt verkrijgen, voegt u de volgende code toe aan de klasse. Het creëert `MSALSilentTokenParameters` een `acquireTokenSilent()`object en roept:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        /**
         
         Acquire a token for an existing account silently
         
         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */
        
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
            
            if let error = error {
                
                let nsError = error as NSError
                
                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                
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

Zodra u een token hebt, kan uw app deze gebruiken in de HTTP-header om een geautoriseerd verzoek in te dienen bij microsoft graph:

| kopteksttoets    | waarde                 |
| ------------- | --------------------- |
| Autorisatie | Toegang-token> aan toonder \< |

Voeg de volgende `ViewController` code toe aan de klasse:

```swift
    func getContentWithToken() {
        
        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
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

Zie [Microsoft Graph API](https://graph.microsoft.com) voor meer informatie over de Microsoft Graph API.

### <a name="use-msal-for-sign-out"></a>MSAL gebruiken voor afmelden

Voeg vervolgens ondersteuning toe voor afmelding.

> [!Important]
> Als u zich afmeldt met MSAL, worden alle bekende informatie over een gebruiker uit de toepassing verwijderd en wordt een actieve sessie op hun apparaat verwijderd wanneer dit is toegestaan door apparaatconfiguratie. U de gebruiker ook optioneel afmelden in de browser.

Als u afmeldingsmogelijkheden wilt toevoegen, `ViewController` voegt u de volgende code toe in de klasse. 

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview
            
            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in
                
                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }
                
                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })
            
        }
    }
```

### <a name="enable-token-caching"></a>Tokencaching inschakelen

Msal slaat standaard de tokens van uw app in de iOS- of macOS-sleutelhanger. 

Tokencache inschakelen:
1. Zorg ervoor dat uw aanvraag correct is ondertekend
2. Ga naar de pagina Xcode-projectinstellingen > **mogelijkheden** > **inschakelen Het delen van sleutelhangers inschakelen**
3. Klik **+** op een volgende vermelding **sleutelhangergroepen:** 3.a `com.microsoft.adalcache` Voor iOS voert u 3.b In voor macOS enter`com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Helpermethoden toevoegen
Voeg de volgende helpermethoden `ViewController` toe aan de klasse om het monster te voltooien.

### <a name="ios-ui"></a>iOS-gebruikersinterface:

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
    
    func updateAccountLabel() {
        
        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }
        
        self.usernameLabel.text = currentAccount.username
    }
    
    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS-gebruikersinterface:

```swift
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
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
    
     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Alleen voor iOS aanvullende apparaatgegevens

Gebruik de volgende code om de huidige apparaatconfiguratie te lezen, inclusief of het apparaat is geconfigureerd als gedeeld:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {
        
        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in
                
                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }
                
                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Toepassingen met meerdere account

Deze app is gemaakt voor één accountscenario. MSAL ondersteunt ook scenario's met meerdere account, maar het vereist wat extra werk van apps. U moet gebruikersinterface maken om gebruikers te helpen selecteren welk account ze willen gebruiken voor elke actie waarvoor tokens nodig zijn. Als alternatief kan uw app een heuristische implementeren om te selecteren welk account u wilt gebruiken door alle accounts van MSAL op te vragen. `accountsFromDeviceForParameters:completionBlock:` [Zie](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:) API bijvoorbeeld

## <a name="test-your-app"></a>Uw app testen

### <a name="run-locally"></a>Lokaal uitvoeren

Bouw en implementeer de app op een testapparaat of simulator. U moet zich kunnen aanmelden en tokens voor Azure AD of persoonlijke Microsoft-accounts kunnen ontvangen.

De eerste keer dat een gebruiker zich aanmeldt bij uw app, wordt deze door de Identiteit van Microsoft gevraagd om in te stemmen met de gevraagde machtigingen.  Hoewel de meeste gebruikers in staat zijn om in te stemmen, hebben sommige Azure AD-tenants de toestemming van de gebruiker uitgeschakeld, waardoor beheerders toestemming moeten geven namens alle gebruikers. Als u dit scenario wilt ondersteunen, registreert u de scopes van uw app in de Azure-portal.

Nadat u zich hebt aangemeld, geeft de app `/me` de gegevens weer die zijn geretourneerd vanuit het eindpunt van Microsoft Graph.

## <a name="get-help"></a>Help opvragen

Ga naar [Help en ondersteuning](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) als u problemen hebt met deze zelfstudie of met het Microsoft-identiteitsplatform.

Help ons het Microsoft-identiteitsplatform te verbeteren. Vertel ons wat u ervan vindt door een korte enquête met twee vragen in te vullen.

> [!div class="nextstepaction"]
> [Enquête van microsoft-identiteitsplatform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
