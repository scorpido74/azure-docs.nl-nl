---
title: Browsers aanpassen & WebViews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het aanpassen van de MSAL iOS/macOS-browserervaring om gebruikers aan te melden.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 3b4362e4c5e69efddfbc99ef0f98ad3c5966165c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450869"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>How to: Browsers en WebViews aanpassen voor iOS/macOS

Een webbrowser is vereist voor interactieve verificatie. Op iOS en macOS 10.15+ gebruikt de Microsoft Authentication Library (MSAL) standaard de systeemwebbrowser (die boven op uw app kan worden weergegeven) om interactieve verificatie uit te doen om gebruikers aan te melden. Het gebruik van de systeembrowser heeft het voordeel dat de Single Sign On (SSO) status wordt gedeeld met andere toepassingen en met webapplicaties.

U de ervaring wijzigen door de configuratie aan te sluiten op andere opties voor het weergeven van webinhoud, zoals:

Alleen voor iOS:

- [SFAuthenticationSessie](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Voor iOS en macOS:

- [ASWebAuthenticationSessie](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL voor macOS `WKWebView` ondersteunt alleen op oudere OS-versies. `ASWebAuthenticationSession`wordt alleen ondersteund op macOS 10.15 en hoger. 

## <a name="system-browsers"></a>Systeembrowsers

Voor `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`, `SFSafariViewController` en worden beschouwd als systeembrowsers. Voor macOS `ASWebAuthenticationSession` is alleen beschikbaar. In het algemeen delen systeembrowsers cookies en andere websitegegevens met de Safari-browsertoepassing.

Standaard detecteert MSAL dynamisch de iOS-versie en selecteert het de aanbevolen systeembrowser die beschikbaar is in die versie. Op iOS 12+ `ASWebAuthenticationSession`zal het . 

### <a name="default-configuration-for-ios"></a>Standaardconfiguratie voor iOS

| Versie | Webbrowser |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSessie |
| iOS 11 | SFAuthenticationSessie |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Standaardconfiguratie voor macOS

| Versie | Webbrowser |
|:-------------:|:-------------:|
| macOS 10.15+ | ASWebAuthenticationSessie |
| andere versies | WKWebView |

Ontwikkelaars kunnen ook een andere systeembrowser selecteren voor MSAL-apps:

- `SFAuthenticationSession`is de iOS 11-versie van `ASWebAuthenticationSession`.
- `SFSafariViewController`is meer algemeen doel en biedt een interface voor het surfen op het web en kan worden gebruikt voor login doeleinden ook. In iOS 9 en 10 worden cookies en andere websitegegevens gedeeld met Safari- maar niet in iOS 11 en hoger.

## <a name="in-app-browser"></a>In-app browser

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) is een in-app browser die webinhoud weergeeft. Het deelt geen cookies of websitegegevens met andere **WKWebView-exemplaren** of met de Safari-browser. WKWebView is een cross-platform browser die beschikbaar is voor zowel iOS als macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie sharing en Single sign-on (SSO) implicaties

De browser die u gebruikt heeft invloed op de SSO-ervaring vanwege de manier waarop deze cookies delen. In de volgende tabellen worden de SSO-ervaringen per browser samengevat.

| Technologie    | Browsertype  | beschikbaarheid van iOS | macOS beschikbaarheid | Deelt cookies en andere gegevens  | Beschikbaarheid van MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSessie](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Systeem | iOS12 en meer | macOS 10.15 en meer | Ja | iOS en macOS 10.15+ | w/ Safari-exemplaren
| [SFAuthenticationSessie](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Systeem | iOS11 en meer | N.v.t. | Ja | Alleen voor iOS |  w/ Safari-exemplaren
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Systeem | iOS11 en meer | N.v.t. | Nee | Alleen voor iOS | Nee**
| **SFSafariViewController** | Systeem | iOS10 | N.v.t. | Ja | Alleen voor iOS |  w/ Safari-exemplaren
| **WKWebView**  | In-app | iOS8 en meer | macOS 10.10 en meer | Nee | iOS en macOS | Nee**

** Om SSO te laten werken, moeten tokens worden gedeeld tussen apps. Hiervoor is een tokencache of broker-toepassing vereist, zoals Microsoft Authenticator voor iOS.

## <a name="change-the-default-browser-for-the-request"></a>De standaardbrowser voor de aanvraag wijzigen

U een in-app browser of een specifieke systeembrowser gebruiken, afhankelijk van `MSALWebviewParameters`uw UX-vereisten, door de volgende eigenschap te wijzigen in:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Wijzigen per interactieve aanvraag

Elke aanvraag kan worden geconfigureerd om de `MSALInteractiveTokenParameters.webviewParameters.webviewType` standaardbrowser te overschrijven door de eigenschap te wijzigen voordat deze wordt doorgeweken naar de `acquireTokenWithParameters:completionBlock:` API.

Daarnaast ondersteunt MSAL het doorgeven `WKWebView` van `MSALInteractiveTokenParameters.webviewParameters.customWebView` een aangepaste door de eigenschap in te stellen.

Bijvoorbeeld:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Als u een aangepaste webweergave gebruikt, worden meldingen gebruikt om de status aan te geven van de weergegeven webinhoud, zoals:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opties

Alle msal ondersteunde webbrowsertypen worden gedeclareerd in het [MSALWebviewType-enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatiestromen en toepassingsscenario's](authentication-flows-app-scenarios.md)
