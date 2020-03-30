---
title: Xamarin-apps migreren met behulp van brokers naar MSAL.NET
titleSuffix: Microsoft identity platform
description: Meer informatie over het migreren van Xamarin iOS-apps die Microsoft Authenticator gebruiken van ADAL.NET naar MSAL.NET.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185832"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>IOS-toepassingen migreren die Microsoft Authenticator gebruiken van ADAL.NET naar MSAL.NET

U gebruikt de Azure Active Directory Authentication Library voor .NET (ADAL.NET) en de iOS-broker. Nu is het tijd om te migreren naar de [Microsoft Authentication Library](msal-overview.md) voor .NET (MSAL.NET), die de broker op iOS vanaf release 4.3 ondersteunt. 

Waar moet je beginnen? Met dit artikel u uw Xamarin iOS-app migreren van ADAL naar MSAL.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al een Xamarin iOS-app hebt die is geïntegreerd met de iOS-broker. Als u dat niet doet, direct naar MSAL.NET en beginnen met de makelaar implementatie daar. Zie [deze documentatie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)voor informatie over het aanroepen van de iOS-broker in MSAL.NET met een nieuwe toepassing.

## <a name="background"></a>Achtergrond

### <a name="what-are-brokers"></a>Wat zijn makelaars?

Brokers zijn applicaties die door Microsoft op Android en iOS. (Zie de [Microsoft Authenticator-app](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) op iOS en Android en de Intune Company Portal-app op Android.) 

Zij maken:

- Eenmalige aanmelding.
- Apparaatidentificatie, die vereist is door sommige [beleid voor voorwaardelijke toegang](../conditional-access/overview.md). Zie [Apparaatbeheer](../conditional-access/concept-conditional-access-conditions.md#device-platforms)voor meer informatie.
- Verificatie van toepassingsidentificatie, die ook vereist is in sommige bedrijfsscenario's. Zie [Intune mobile application management (MAM) voor](https://docs.microsoft.com/intune/mam-faq)meer informatie.

## <a name="migrate-from-adal-to-msal"></a>Migreren van ADAL naar MSAL

### <a name="step-1-enable-the-broker"></a>Stap 1: De makelaar inschakelen

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL tegenhanger:</td></tr>
<tr><td>
In ADAL.NET werd broker-ondersteuning ingeschakeld op basis van een context per verificatie. Het is standaard uitgeschakeld. Je moest een 

`useBroker`vlag te waar `PlatformParameters` in de constructor om de makelaar te bellen:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Stel in de platformspecifieke code in dit voorbeeld in de paginarenderer voor iOS de`useBroker` 
vlag naar true:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Neem vervolgens de parameters op in het tokengesprek voor aanschaffen:
```csharp
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
In MSAL.NET is broker support ingeschakeld per PublicClientApplication. Het is standaard uitgeschakeld. Gebruik de 

`WithBroker()`parameter (standaard ingesteld op true) om de makelaar te bellen:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
In het werven token call:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Stap 2: Een UIViewController instellen()
In ADAL.NET bent u geslaagd in een `PlatformParameters`UIViewController als onderdeel van . (Zie het voorbeeld in stap 1.) In MSAL.NET wordt een objectvenster gebruikt om ontwikkelaars meer flexibiliteit te geven, maar dit is niet vereist in regulier iOS-gebruik. Als u de makelaar wilt gebruiken, stelt u het objectvenster in om antwoorden van de makelaar te verzenden en te ontvangen. 
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL tegenhanger:</td></tr>
<tr><td>
Een UIViewController wordt doorgegeven aan 

`PlatformParameters`in het iOS-specifieke platform.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
In MSAL.NET doet u twee dingen om het objectvenster voor iOS in te stellen:

1. In `AppDelegate.cs`, `App.RootViewController` ingesteld `UIViewController()`op een nieuwe . Deze opdracht zorgt ervoor dat er een UIViewController is met de oproep naar de broker. Als deze niet correct is ingesteld, krijgt u mogelijk de fout:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Gebruik en geef in `.WithParentActivityOrWindow(App.RootViewController)`de verwijzing naar het objectvenster dat u gebruikt in de aanroep AcquireTokenInteractive.

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
In het werven token call:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken om de terugbelfunctie af te handelen
Zowel ADAL en MSAL bellen de makelaar, en de makelaar `OpenUrl` op `AppDelegate` zijn beurt roept terug naar uw aanvraag via de methode van de klasse. Zie [deze documentatie](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)voor meer informatie.

Er zijn hier geen veranderingen tussen ADAL.NET en MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Stap 4: Een URL-schema registreren
ADAL.NET en MSAL.NET URL's gebruiken om de makelaar aan te roepen en de reactie van de makelaar terug te sturen naar de app. Registreer het URL-schema in het `Info.plist` bestand voor uw app als volgt:

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL tegenhanger:</td></tr>
<tr><td>
Het URL-schema is uniek voor uw app.
</td><td>
Kan 

`CFBundleURLSchemes`de naam moet 

`msauth.`

als voorvoegsel, gevolgd door uw`CFBundleURLName`

Bijvoorbeeld: `$"msauth.(BundleId")`

```csharp
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
> Dit URL-schema wordt onderdeel van de omleiding uri die wordt gebruikt om de app uniek te identificeren wanneer deze het antwoord van de broker ontvangt.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Stap 5: De broker-id toevoegen aan de sectie LSApplicationQueriesSchemes

ADAL.NET en MSAL.NET `-canOpenURL:` beide gebruiken om te controleren of de makelaar is geïnstalleerd op het apparaat. Voeg als volgt de juiste id voor de iOS-broker toe aan de sectie LSApplicationQueriesSchemes van het info.plist-bestand:

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL tegenhanger:</td></tr>
<tr><td>
Gebruikt 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Gebruikt 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Stap 6: Registreer uw omleiding URI in de portal

ADAL.NET en MSAL.NET beide een extra vereiste op de redirect URI toe te voegen wanneer het zich richt op de makelaar. Registreer de omleiding URI met uw toepassing in de portal.
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL tegenhanger:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Voorbeeld: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Voorbeeld:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Zie [De broker gebruiken in Xamarin.iOS-toepassingen](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app)voor meer informatie over het registreren van de omleidingsURI in de portal.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Xamarin iOS-specifieke overwegingen met MSAL.NET](msal-net-xamarin-ios-considerations.md). 
