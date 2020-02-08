---
title: Quick start voor micro soft Identity platform iOS en macOS | Azure
description: Meer informatie over het aanmelden van gebruikers en het Microsoft Graph van query's in een iOS-of macOS-toepassing.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 4dea0feb5d5a1cb42640b1fc05bb185e970ae8af
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084491"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Snelstartgids: gebruikers aanmelden en de Microsoft Graph-API aanroepen vanuit een iOS-of macOS-app

Deze Snelstartgids bevat een code voorbeeld dat laat zien hoe een systeem eigen iOS-of macOS-toepassing het micro soft Identity-platform kan gebruiken voor het aanmelden van persoonlijke, werk-en school accounts, het verkrijgen van een toegangs token en het aanroepen van de Microsoft Graph-API.

Deze Quick start is van toepassing op iOS-en macOS-apps. Sommige stappen zijn alleen nodig voor iOS-apps. Bij deze stappen wordt ervan uitgegaan dat ze alleen voor iOS zijn.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Vereisten**
> * XCode 10 +
> * iOS 10+ 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens de voorbeeldcode](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Optie 1: uw app registreren en automatisch configureren en vervolgens het code voorbeeld downloaden
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Als u uw app wilt registreren,
> 1. Ga naar het deel venster nieuwe [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) .
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: de toepassing en voorbeeldcode registreren en handmatig configureren
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://aka.ms/MobileAppReg) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app wanneer deze zich aanmelden of toestemming voor uw app geven.
>      - Andere configuraties op deze pagina overs Laan.
>      - Selecteer `Register`.
> 1. Selecteer in de sectie **beheren** de optie `Authentication` > `Add Platform` > `iOS`.
>      - Voer de ***bundel-id*** in voor uw toepassing. De bundel-id is een unieke teken reeks die uw toepassing uniek identificeert, bijvoorbeeld `com.<yourname>.identitysample.MSALMacOS`. Noteer de waarde die u gebruikt.
>      - Houd er rekening mee dat de iOS-configuratie ook van toepassing is op macOS-toepassingen.
> 1. Selecteer `Configure` en sla de details van de ***MSAL-configuratie*** op verderop in deze Quick Start.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Stap 1: Uw toepassing configureren
> Het code voorbeeld voor deze Quick Start werkt alleen als u een omleidings-URI toevoegt die compatibel is met de auth Broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-ios/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken

#### <a name="step-2-download-the-sample-project"></a>Stap 2: het voorbeeld project downloaden

- [Het code voorbeeld voor iOS downloaden](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Het code voorbeeld voor macOS downloaden](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Stap 3: afhankelijkheden installeren

Navigeer in een Terminal venster naar de map met het gedownloade code voorbeeld en voer `pod install` uit om de meest recente MSAL-bibliotheek te installeren.

#### <a name="step-4-configure-your-project"></a>Stap 4: uw project configureren

> [!div renderon="docs"]
> Als u optie 1 hierboven hebt geselecteerd, kunt u deze stappen overs Laan. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Pak het zip-bestand uit en open het project in XCode.
> 1. Bewerk **View Controller. Swift** en vervang de regel die begint met ' laat kClientID ' door het volgende code fragment. Vergeet niet om de waarde voor `kClientID` bij te werken met de client-ID die u hebt opgeslagen tijdens het registreren van uw app in de portal eerder in de Quick Start:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Open de project instellingen. Voer in de sectie **identiteit** de **bundel-id** in die u hebt ingevoerd in de portal.
> 1. Alleen voor iOS: Klik met de rechter muisknop op **info. plist** en selecteer **openen als** **bron code** > .
> 1. Voor alleen iOS, onder het hoofd knooppunt dict, vervangt u `CFBundleURLSchemes` door de ***bundel-id*** die u hebt ingevoerd in de portal.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. Bouw & de app uit te voeren.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Deze Quick Start ondersteunt Enter_the_Supported_Account_Info_Here.
> [!div renderon="docs"]
>
> 1. Pak het zip-bestand uit en open het project in XCode.
> 1. Bewerk **View Controller. Swift** en vervang de regel die begint met ' laat kClientID ' door het volgende code fragment. Vergeet niet om de waarde voor `kClientID` bij te werken met de clientID die u hebt opgeslagen tijdens het registreren van uw app in de portal eerder in deze Quick Start:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Open de project instellingen. Voer in de sectie **identiteit** de **bundel-id** in die u hebt ingevoerd in de portal.
> 1. Alleen voor iOS: Klik met de rechter muisknop op **info. plist** en selecteer **openen als** **bron code** > .
> 1. Voor alleen iOS, onder het hoofd knooppunt dict, vervangt u `Enter_the_bundle_Id_Here` door de ***bundel-id*** die u in de portal hebt gebruikt.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Bouw & de app uit te voeren. 

## <a name="more-information"></a>Meer informatie

Lees deze secties voor meer informatie over deze snelstart.

### <a name="get-msal"></a>MSAL ophalen

MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt om toegang te krijgen tot een API die wordt beveiligd door micro soft Identity platform. U kunt MSAL toevoegen aan uw toepassing met behulp van het volgende proces:

```
$ vi Podfile

```
Voeg het volgende toe aan deze podfile (met het doel van het project):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Voer de CocoaPods-installatie opdracht uit:

```pod install```

### <a name="initialize-msal"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```swift
import MSAL
```

Vervolgens initialiseert u MSAL met de volgende code:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Waar: ||
> |---------|---------|
> | `clientId` | De toepassings-id van de toepassing die is geregistreerd in *portal.azure.com* |
> | `authority` | Het micro soft Identity platform-eind punt. In de meeste gevallen is dit *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | De omleidings-URI van de toepassing. U kunt Nil door geven om de standaard waarde of uw aangepaste omleidings-URI te gebruiken. |

### <a name="for-ios-only-additional-app-requirements"></a>Alleen voor iOS, extra app-vereisten

Uw app moet ook het volgende in uw `AppDelegate`hebben. Hiermee kan de MSAL SDK-reactie van de auth Broker-app worden verwerkt wanneer u verificatie doet.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> Als u op iOS 13 + een `UISceneDelegate` in plaats van `UIApplicationDelegate`, plaatst u deze code in de `scene:openURLContexts:` call back in plaats daarvan (Zie [de documentatie van Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Als u zowel UISceneDelegate als UIApplicationDelegate voor compatibiliteit met oudere iOS-versies ondersteunt, moet MSAL call back worden geplaatst op beide locaties.

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

Ten slotte moet uw app in uw ***info. plist*** naast de `CFBundleURLTypes`een `LSApplicationQueriesSchemes` vermelding bevatten. Dit is inclusief het voor beeld. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Aanmeldings tokens voor gebruikers & aanvragen

MSAL biedt twee methoden voor het verkrijgen van tokens: `acquireToken` en `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: een token interactief ophalen

In sommige situaties moeten gebruikers communiceren met het micro soft Identity-platform. In dergelijke gevallen kan de eind gebruiker verplicht zijn of haar account selecteren, hun referenties invoeren of toestemming geven voor de machtigingen van uw app. Bijvoorbeeld: 

* De eerste keer dat gebruikers zich aanmelden bij de toepassing
* Als een gebruiker het wacht woord opnieuw instelt, moeten ze hun referenties invoeren 
* Wanneer uw toepassing voor de eerste keer toegang tot een resource vraagt
* Wanneer MFA of een ander beleid voor voorwaardelijke toegang is vereist

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Waar:||
> |---------|---------|
> | `scopes` | Bevat de bereiken die worden aangevraagd (dat wil zeggen, `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-Api's (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: een toegangs token op de achtergrond ophalen

Voor apps mogen gebruikers zich niet elke keer aanmelden wanneer ze een token aanvragen. Als de gebruiker al is aangemeld, kunnen apps tokens op de achtergrond aanvragen. 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |Waar: ||
> |---------|---------|
> | `scopes` | Bevat de bereiken die worden aangevraagd (dat wil zeggen, `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-Api's (`api://<Application ID>/access_as_user`) |
> | `account` | Het account waarvoor een token wordt aangevraagd. Deze Quick start is over een enkele account toepassing. Als u een app met meerdere accounts wilt maken, moet u de logica definiëren om te bepalen welk account moet worden gebruikt voor token aanvragen met behulp van `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Volgende stappen

Probeer de iOS-zelf studie voor een volledige stapsgewijze hand leiding voor het bouwen van toepassingen, inclusief een volledige uitleg van deze Snelstartgids.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Meer informatie over het maken van de toepassing die wordt gebruikt in deze Quick Start

> [!div class="nextstepaction"]
> [iOS-zelfstudie voor Graph API-aanroepen](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
