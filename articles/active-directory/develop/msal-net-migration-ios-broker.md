---
title: Xamarin iOS-toepassingen migreren met behulp van Microsoft Authenticator van ADAL.NET naar MSAL.NET | Azure
description: Meer informatie over het migreren van Xamarin iOS-toepassingen met Microsoft Authenticator van de Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) naar de micro soft Authentication Library voor .NET (MSAL.NET)
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875651"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>IOS-toepassingen migreren met Microsoft Authenticator van ADAL.NET naar MSAL.NET

U hebt ADAL.NET en de iOS-Broker gebruikt, en het is tijd om te migreren naar MSAL.NET [micro soft-verificatie bibliotheek](msal-overview.md), die de Broker op Ios van release 4,3 en hoger ondersteunt. 

Waar beginnen? In dit artikel vindt u informatie over het migreren van uw Xamarin iOS-app van ADAL naar MSAL.

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u al een Xamarin iOS-app hebt die is geïntegreerd met de iOS-Broker. Als dat niet het geval is, kunt u het beste rechtstreeks naar MSAL.NET gaan en daar de Broker-implementatie starten. Raadpleeg [deze documentatie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) voor meer informatie over het aanroepen van de IOS-broker in MSAL.net met een nieuwe toepassing.

## <a name="background"></a>Achtergrond

### <a name="what-are-brokers"></a>Wat zijn makelaars?

Makelaars zijn toepassingen die door micro soft worden meegeleverd op Android en iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) op Ios en Android intune-bedrijfsportal op Android). 

Ze inschakelen:

- Eenmalige aanmelding,
- Apparaat-id, die wordt vereist door een [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) (Zie [Apparaatbeheer](../conditional-access/conditions.md#device-platforms))
- Verificatie van de toepassings-id, ook vereist in een aantal bedrijfs scenario's (Zie voor exemplaar [intune Mobile Application Management of mam](https://docs.microsoft.com/intune/mam-faq))

## <a name="migrate-from-adal-to-msal"></a>Migreren van ADAL naar MSAL

### <a name="step-1-enable-the-broker"></a>Stap 1: De Broker inschakelen

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
In ADAL.NET is Broker-ondersteuning ingeschakeld op basis van context per verificatie, het is standaard uitgeschakeld. U moest een `useBroker` vlag instellen op True in de `PlatformParameters` constructor om Broker aan te roepen:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Daarnaast stelt u in de platformspecifieke code, in dit voor beeld in de pagina renderer voor iOS, het`useBroker` 
vlag toevoegen aan waar:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Neem vervolgens de para meters op in de aanroep voor het verkrijgen van een token:
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
In MSAL.NET is Broker-ondersteuning ingeschakeld op basis van een per-open bare client toepassing. Deze optie is standaard uitgeschakeld. Als u deze wilt inschakelen, gebruikt u: 

`WithBroker()`para meter (standaard ingesteld op True) om Broker aan te roepen:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
In de aanroep voor het verkrijgen van een token:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Stap 2: Een UIViewController instellen ()
In ADAL.NET is u in de UIViewController door gegeven als onderdeel van de PlatformParameters (Zie voor beeld in stap 1). In MSAL.NET wordt echter een object venster gebruikt om de ontwikkel aars meer flexibiliteit te geven, maar dit is niet vereist in het gebruik van gewoon iOS. Als u de Broker echter wilt gebruiken, moet u het object venster instellen om antwoorden van de Broker te verzenden en te ontvangen. 
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
De UIViewController wordt door gegeven aan de PlatformParamters in het specifieke iOS-platform.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
In MSAL.NET moet u twee dingen doen om het object venster voor iOS in te stellen:

1) Stel `AppDelegate.cs`in de `App.RootViewController` in op een nieuw `UIViewController()`. Deze toewijzing zorgt ervoor dat er een UIViewController is met de aanroep naar de Broker. Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Gebruik op de aanroep AcquireTokenInteractive de`.WithParentActivityOrWindow(App.RootViewController)`
en geeft u de verwijzing naar het object venster you'will gebruiken.

**Bijvoorbeeld:**

In `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
In `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
In de aanroep voor het verkrijgen van een token:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken voor het afhandelen van de call back
Zowel ADAL als MSAL zullen de Broker aanroepen, en Broker zal op zijn beurt terugbellen naar uw toepassing via de `OpenUrl` methode van de `AppDelegate` -klasse. Meer informatie is [hier](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback) beschikbaar

: heavy_check_mark:**Er zijn hier geen wijzigingen tussen ADAL.net en MSAL.net**

### <a name="step-4-register-a-url-scheme"></a>Stap 4: Een URL-schema registreren
ADAL.NET en MSAL.NET gebruiken Url's om de Broker aan te roepen en retour neren de Broker respons terug naar de app. Registreer het URL-schema in `Info.plist` het bestand voor uw app als volgt:

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
Het URL-schema is uniek voor uw app.
</td><td>
Kan 

`CFBundleURLSchemes`naam moet bevatten 

`msauth.`

Als voor voegsel, gevolgd door uw`CFBundleURLName`

Bijvoorbeeld: `$"msauth.(BundleId")`

```CSharp
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

> [!NOTE]
>  Dit URL-schema wordt onderdeel van de RedirectUri die wordt gebruikt voor het uniek identificeren van de app bij het ontvangen van het antwoord van de Broker

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Stap 5: LSApplicationQueriesSchemes

ADAL.net en MSAL.net beide gebruiken `-canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. Voeg als volgt de juiste id voor de iOS-Broker toe aan de sectie LSApplicationQueriesSchemes van het bestand info. plist: 
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
Gebruik 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Gebruik 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Stap 6: Registreer u RedirectUri in de portal

ADAL.NET en MSAL.NET voegen beide extra vereisten toe aan de redirectUri wanneer de doel Broker. Registreer de omleidings-URI met uw toepassing in de portal.
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`Hierbij`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

voorbeeld:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Zie voor meer informatie over het registreren van de redirectUri in de portal [de Broker gebruiken in Xamarin. IOS-toepassingen](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Xamarin IOS-specifieke overwegingen met MSAL.net](msal-net-xamarin-ios-considerations.md). 
