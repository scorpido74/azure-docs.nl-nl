---
title: Microsoft identity platform iOS en macOS quickstart | Azure
description: Meer informatie over het aanmelden van gebruikers en het opvragen van Microsoft Graph in een iOS- of macOS-toepassing.
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
ms.openlocfilehash: 090f59c4074ca2613c3bd32030b0869a1cd4e9d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80129029"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Snelstart: gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een iOS- of macOS-app

Deze quickstart bevat een codevoorbeeld dat laat zien hoe een native iOS- of macOS-toepassing het Microsoft-identiteitsplatform kan gebruiken om persoonlijke, werk- en schoolaccounts aan te melden, een toegangstoken te krijgen en de Microsoft Graph API aan te roepen.

Deze quickstart geldt voor zowel iOS- als macOS-apps. Sommige stappen zijn alleen nodig voor iOS-apps. Die stappen roepen dat ze alleen voor iOS.

![Laat zien hoe de voorbeeld-app die door deze quickstart wordt gegenereerd, werkt](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Vereisten**
> * XCode 10+
> * iOS 10+ 
> * macOS 10.12+

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens de voorbeeldcode](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Optie 1: Uw app registreren en automatisch configureren en vervolgens het codevoorbeeld downloaden
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Uw app registreren,
> 1. Ga naar de nieuwe [Azure-portal - deelvenster App-registraties.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs)
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Navigeer naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://aka.ms/MobileAppReg)
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **Naam** een betekenisvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app wanneer ze zich aanmelden of toestemming geven voor uw app.
>      - Sla andere configuraties op deze pagina over.
>      - Selecteer `Register`.
> 1. Selecteer **in** de `Authentication`  >  `Add Platform`  >  `iOS`sectie Beheren de optie .
>      - Voer de ***bundel-id*** voor uw toepassing in. De bundel-id is slechts een unieke tekenreeks die `com.<yourname>.identitysample.MSALMacOS`uw toepassing uniek identificeert, bijvoorbeeld . Noteer de waarde die u gebruikt.
>      - Houd er rekening mee dat de iOS-configuratie ook van toepassing is op macOS-toepassingen.
> 1. Selecteer `Configure` en sla de ***MSAL-configuratiegegevens*** op voor later in deze quickstart.
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Stap 1: Uw toepassing configureren
> Voor het codevoorbeeld voor deze quickstart moet u een omleidingsURI toevoegen die compatibel is met de Auth-broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-ios/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken

#### <a name="step-2-download-the-sample-project"></a>Stap 2: Het voorbeeldproject downloaden

- [Het codevoorbeeld voor iOS downloaden](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Het codevoorbeeld voor macOS downloaden](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Stap 3: Afhankelijkheden installeren

Navigeer in een terminalvenster naar de map met `pod install` het gedownloade codevoorbeeld en voer uit om de nieuwste MSAL-bibliotheek te installeren.

#### <a name="step-4-configure-your-project"></a>Stap 4: Uw project configureren

> [!div renderon="docs"]
> Als u optie 1 hierboven hebt geselecteerd, u deze stappen overslaan. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Pak het zip-bestand uit en open het project in XCode.
> 1. Bewerk **ViewController.swift** en vervang de lijn te beginnen met 'laat kClientID' met de volgende code fragment. Vergeet niet om `kClientID` de waarde bij te werken met de client-id die u hebt opgeslagen toen u uw app eerder in de snelstart in de portal registreerde:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Bewerk **ViewController.swift** en vervang de lijn te beginnen met 'laat kAuthority' met de volgende code fragment:
>    ```swift
>    let kAuthority = "Enter_the_Authority_Endpoint_Host_HereEnter_the_Tenant_Info_Here"
>    ```
> 1. Bewerk **ViewController.swift** en vervang de lijn te beginnen met 'laat kGraphEndpoint' met de volgende code fragment:
>    ```swift
>    let kGraphEndpoint = "Enter_the_MS_Graph_Endpoint_Host_Here"
>    ```
> 1. Open de projectinstellingen. Voer **in** de sectie Identiteit de **bundel-id** in die u in de portal hebt ingevoerd.
> 1. Klik alleen voor iOS op **Info.plist** en selecteer **Openen als** > **broncode**.
> 1. Alleen voor iOS onder het dict-hoofdknooppunt vervangen `CFBundleURLSchemes` door de ***bundel-id*** die u in de portal hebt ingevoerd.
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
> 1. Bouw & de app uit te voeren!
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here
> [!div renderon="docs"]
>
> 1. Pak het zip-bestand uit en open het project in XCode.
> 1. Bewerk **ViewController.swift** en vervang de lijn te beginnen met 'laat kClientID' met de volgende code fragment. Vergeet niet om `kClientID` de waarde bij te werken met de clientID die u hebt opgeslagen toen u uw app eerder in deze quickstart in de portal registreerde:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Als u een app bouwt voor nationale clouds van [Azure AD,](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)vervangt u de lijn die begint met 'laat kGraphEndpoint' en 'laat kAuthority' met de juiste eindpunten. Gebruik standaardwaarden voor algemene toegang:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Andere eindpunten worden [hier](https://docs.microsoft.com/graph/deployments#app-registration-and-token-service-root-endpoints)gedocumenteerd. Als u bijvoorbeeld de quickstart met Azure AD Germany wilt uitvoeren, gebruikt u het volgende:
>     ```objective-c
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Open de projectinstellingen. Voer **in** de sectie Identiteit de **bundel-id** in die u in de portal hebt ingevoerd.
> 1. Klik alleen voor iOS op **Info.plist** en selecteer **Openen als** > **broncode**.
> 1. Voor iOS alleen, onder de dict `Enter_the_bundle_Id_Here` root node, vervangen door de ***Bundle Id*** die u gebruikt in de portal.
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
> 1. Bouw & de app uit te voeren! 

## <a name="more-information"></a>Meer informatie

Lees deze secties voor meer informatie over deze snelstart.

### <a name="get-msal"></a>MsAL krijgen

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) is de bibliotheek die wordt gebruikt om gebruikers aan te melden en tokens aan te vragen die worden gebruikt om toegang te krijgen tot een API die wordt beschermd door het identiteitsplatform van Microsoft. U kunt MSAL toevoegen aan uw toepassing met behulp van het volgende proces:

```
$ vi Podfile

```
Voeg het volgende toe aan deze podfile (met het doel van uw project):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

De installatieopdracht CocoaPods uitvoeren:

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
> | `authority` | Het eindpunt van het Microsoft-identiteitsplatform. In de meeste gevallen is dit *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | De omleiding URI van de toepassing. U 'nihil' doorgeven om de standaardwaarde te gebruiken, of uw aangepaste omleiding URI. |

### <a name="for-ios-only-additional-app-requirements"></a>Alleen voor iOS extra app-vereisten

Uw app moet ook het `AppDelegate`volgende in uw . Hiermee kan MSAL SDK tokenrespons verwerken vanuit de Auth broker-app wanneer u verificatie doet.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> Op iOS 13+ plaats `UISceneDelegate` je `UIApplicationDelegate`deze code in `scene:openURLContexts:` plaats daarvan in de callback (Zie [de documentatie van Apple).](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)
> Als u zowel UISceneDelegate als UIApplicationDelegate ondersteunt voor compatibiliteit met oudere iOS, moet MSAL-terugroepen op beide plaatsen worden geplaatst.

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

Ten slotte moet uw `LSApplicationQueriesSchemes` app een vermelding in uw `CFBundleURLTypes` ***Info.plist*** hebben naast de . Het monster wordt geleverd met deze inbegrepen. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Aanmelden gebruikers & tokens aanvragen

MSAL biedt twee methoden voor het verkrijgen van tokens: `acquireToken` en `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Ontvang een token interactief

In sommige situaties moeten gebruikers communiceren met het identiteitsplatform van Microsoft. In deze gevallen kan de eindgebruiker worden verplicht om zijn account te selecteren, zijn of zijn of haar referenties in te voeren of in te stemmen met de machtigingen van uw app. Bijvoorbeeld: 

* De eerste keer dat gebruikers zich aanmelden bij de toepassing
* Als een gebruiker zijn wachtwoord opnieuw instelt, moet hij zijn of haar referenties invoeren 
* Wanneer uw toepassing voor het eerst toegang tot een bron aanvraagt
* Wanneer MFA of ander beleid voor voorwaardelijke toegang vereist is

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Waar:||
> |---------|---------|
> | `scopes` | Bevat de aanvragende scopes (dat wil `[ "user.read" ]` `[ "<Application ID URL>/scope" ]` zeggen voor Microsoft`api://<Application ID>/access_as_user`Graph of voor aangepaste web-API's ( ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Ontvang een access token in stilte

Apps moeten niet vereisen dat hun gebruikers zich elke keer aanmelden wanneer ze een token aanvragen. Als de gebruiker zich al heeft aangemeld, kunnen apps met deze methode stilletjes tokens aanvragen. 

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in
            
   guard let account = currentAccount else {
      return
   }
            
   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Waar: ||
> |---------|---------|
> | `scopes` | Bevat de aanvragende scopes (dat wil `[ "user.read" ]` `[ "<Application ID URL>/scope" ]` zeggen voor Microsoft`api://<Application ID>/access_as_user`Graph of voor aangepaste web-API's ( ) |
> | `account` | Het account waarvoor een token wordt aangevraagd. Deze quickstart gaat over één accounttoepassing. Als u een app met meerdere accounten wilt bouwen, moet u logica definiëren `accountsFromDeviceForParameters:completionBlock:` om te bepalen welk account u wilt gebruiken voor tokenaanvragen met behulp van en correct doorgeven`accountIdentifier` |

## <a name="next-steps"></a>Volgende stappen

Probeer de zelfstudie voor iOS en macOS voor een complete stapsgewijze handleiding voor het bouwen van applicaties, inclusief een volledige uitleg van deze quickstart.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>Meer informatie over het maken van de toepassing die in deze quickstart wordt gebruikt

> [!div class="nextstepaction"]
> [Zelfstudie van Call Graph API voor iOS en macOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Help ons het Microsoft-identiteitsplatform te verbeteren. Vertel ons wat u ervan vindt door een korte enquête met twee vragen in te vullen.

> [!div class="nextstepaction"]
> [Enquête van microsoft-identiteitsplatform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
