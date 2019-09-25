---
title: Migreer Xamarin iOS-toepassingen die gebruikmaken van Microsoft Authenticator van ADAL.NET naar MSAL.NET | Azure
description: Meer informatie over het migreren van Xamarin iOS-toepassingen die gebruikmaken van Microsoft Authenticator van de Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) naar de micro soft-verificatie bibliotheek voor .NET (MSAL.NET).
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
ms.openlocfilehash: fdfb2d7d33111f1adf998cd75446576d2010a365
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257783"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>IOS-toepassingen migreren die gebruikmaken van Microsoft Authenticator van ADAL.NET naar MSAL.NET

U gebruikt de Azure Active Directory Authentication Library voor .NET (ADAL.NET) en de iOS-Broker. Nu is het tijd om te migreren naar de [micro soft Authentication Library](msal-overview.md) voor .net (MSAL.net), die ondersteuning biedt voor de Broker op Ios vanaf versie 4,3 en hoger. 

Waar moet u beginnen? Dit artikel helpt u bij het migreren van uw Xamarin iOS-app van ADAL naar MSAL.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u al een Xamarin iOS-app hebt die is geïntegreerd met de iOS-Broker. Als dat niet het geval is, gaat u rechtstreeks naar MSAL.NET en begint u met het implementeren van de Broker. Raadpleeg [deze documentatie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)voor meer informatie over het aanroepen van de IOS-broker in MSAL.net met een nieuwe toepassing.

## <a name="background"></a>Achtergrond

### <a name="what-are-brokers"></a>Wat zijn makelaars?

Makelaars zijn toepassingen die door micro soft worden verschaft op Android en iOS. (Zie de app [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) op Ios en Android en de intune-bedrijfsportal-app op Android.) 

Ze inschakelen:

- Eenmalige aanmelding.
- Apparaat-id, die wordt vereist door een [beleid voor voorwaardelijke toegang](../conditional-access/overview.md). Zie [Apparaatbeheer](../conditional-access/conditions.md#device-platforms)voor meer informatie.
- Verificatie van de toepassings-id. Dit is ook vereist in sommige bedrijfs scenario's. Zie [intune-Mobile Application Management (mam) (Engelstalig)](https://docs.microsoft.com/intune/mam-faq)voor meer informatie.

## <a name="migrate-from-adal-to-msal"></a>Migreren van ADAL naar MSAL

### <a name="step-1-enable-the-broker"></a>Stap 1: De Broker inschakelen

<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
In ADAL.NET is Broker-ondersteuning ingeschakeld op basis van context per verificatie. Het is standaard uitgeschakeld. U moest een 

`useBroker`vlag toevoegen aan True in `PlatformParameters` de constructor om de Broker aan te roepen:

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
In MSAL.NET is de ondersteuning van Broker ingeschakeld op basis van één PublicClientApplication. Het is standaard uitgeschakeld. Als u deze wilt inschakelen, gebruikt u de 

`WithBroker()`para meter (standaard ingesteld op True) om de Broker aan te roepen:

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
In ADAL.NET hebt u een UIViewController door gegeven als onderdeel van `PlatformParameters`. (Zie het voor beeld in stap 1.) In MSAL.NET wordt een object venster gebruikt om ontwikkel aars meer flexibiliteit te geven, maar dit is niet vereist in het gebruik van gewoon iOS. Als u de Broker wilt gebruiken, stelt u het object venster in om antwoorden te verzenden en te ontvangen van de Broker. 
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>
Er wordt een UIViewController door gegeven aan 

`PlatformParameters`in het iOS-specifieke platform.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
In MSAL.NET voert u twee dingen uit om het object venster voor iOS in te stellen:

1. `UIViewController()`Stel `AppDelegate.cs` in`App.RootViewController` op een nieuw. Deze toewijzing zorgt ervoor dat er een UIViewController is met de aanroep naar de Broker. Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Gebruik `.WithParentActivityOrWindow(App.RootViewController)`in de AcquireTokenInteractive-aanroep en geef in de verwijzing naar het object venster dat u gaat gebruiken.

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
Zowel ADAL als MSAL bellen de Broker en de broker in zet de app terug naar uw toepassing via de `OpenUrl` methode van de `AppDelegate` klasse. Raadpleeg [deze documentatie](msal-net-use-brokers-with-xamarin-apps.md#step-2-update-appdelegate-to-handle-the-callback)voor meer informatie.

Er zijn hier geen wijzigingen tussen ADAL.NET en MSAL.NET.

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
> Dit URL-schema wordt onderdeel van de omleidings-URI die wordt gebruikt om de app op unieke wijze te identificeren wanneer deze het antwoord van de Broker ontvangt.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Stap 5: De Broker-id toevoegen aan de sectie LSApplicationQueriesSchemes

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

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Stap 6: De omleidings-URI in de portal registreren

ADAL.NET en MSAL.NET voegen zowel een extra vereiste voor de omleidings-URI toe als deze de Broker doel heeft. Registreer de omleidings-URI met uw toepassing in de portal.
<table>
<tr><td>Huidige ADAL-code:</td><td>MSAL-tegen hanger:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Voorbeeld: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Voorbeeld:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Zie [de Broker gebruiken in Xamarin. IOS-toepassingen](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app)voor meer informatie over het registreren van de omleidings-URI in de portal.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Xamarin IOS-specifieke overwegingen met MSAL.net](msal-net-xamarin-ios-considerations.md). 
