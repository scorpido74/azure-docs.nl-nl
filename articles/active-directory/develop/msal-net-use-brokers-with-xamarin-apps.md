---
title: Brokers gebruiken met Xamarin, iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het migreren van Xamarin iOS-toepassingen die Microsoft Authenticator van de Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) kunnen gebruiken naar de micro soft Authentication Library voor .NET (MSAL.NET)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49198909da103debd77fcf0d630e0fa16c1e4448
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424226"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator-of Microsoft Intune-Bedrijfsportal gebruiken in Xamarin-toepassingen

Op Android en iOS kunnen brokers als Microsoft Authenticator of Microsoft Intune Bedrijfsportal inschakelen (alleen Android):

- Eenmalige aanmelding (SSO). Uw gebruikers hoeven zich niet aan te melden bij elke toepassing.
- Apparaat-id. De Broker heeft toegang tot het certificaat van het apparaat, dat is gemaakt op het apparaat toen het is gekoppeld aan de werk plek.
- Verificatie van de toepassings-id. Wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven en wordt deze door de Broker gecontroleerd.

Om een van deze functies in te scha kelen, moeten ontwikkel aars van toepassingen de para meter `WithBroker()` gebruiken wanneer ze de `PublicClientApplicationBuilder.CreateApplication`-methode aanroepen. `.WithBroker()` is standaard ingesteld op waar. Ontwikkel aars moeten de stappen hier ook volgen voor [IOS](#brokered-authentication-for-ios) -of [Android](#brokered-authentication-for-android) -toepassingen.

## <a name="brokered-authentication-for-ios"></a>Brokered authenticatie voor iOS

Volg deze stappen om uw Xamarin. iOS-app in te scha kelen voor communicatie met de app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen
Broker-ondersteuning is ingeschakeld per PublicClientApplication. Het is standaard uitgeschakeld. Gebruik de para meter `WithBroker()` (standaard ingesteld op True) wanneer u de PublicClientApplication maakt via de PublicClientApplicationBuilder.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Stap 2: toegang tot sleutel hanger inschakelen

Als u de toegang tot de sleutel keten wilt inschakelen, moet uw toepassing een toegangs groep voor de sleutel hanger hebben. U kunt de `WithIosKeychainSecurityGroup()`-API gebruiken om de toegangs groep voor de sleutel hanger in te stellen wanneer u uw toepassing maakt:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Zie [toegang tot sleutel hanger inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)voor meer informatie.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken voor het afhandelen van de call back
Als de micro soft Authentication Library voor .NET (MSAL.NET) de Broker aanroept, wordt de Broker op zijn beurt teruggebeld naar uw toepassing via de `OpenUrl` methode van de `AppDelegate`-klasse. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL.NET terug aan te roepen. Als u deze samen werking wilt inschakelen, werkt u het `AppDelegate.cs` bestand bij om de volgende methode te overschrijven.

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

Deze methode wordt aangeroepen telkens wanneer de toepassing wordt gestart. Het wordt gebruikt als een kans om de reactie van de Broker te verwerken en het verificatie proces te volt ooien dat door MSAL.NET wordt gestart.

### <a name="step-4-set-a-uiviewcontroller"></a>Stap 4: Stel een UIViewController in ()
U moet nog steeds in `AppDelegate.cs`een object venster instellen. Normaal gesp roken hoeft u met Xamarin iOS geen object venster in te stellen. Als u antwoorden wilt verzenden en ontvangen van de Broker, hebt u een object venster nodig. 

Hiervoor moet u twee dingen doen. 
1. Stel in `AppDelegate.cs`de `App.RootViewController` in op een nieuwe `UIViewController()`. Deze toewijzing zorgt ervoor dat er een UIViewController is met de aanroep naar de Broker. Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Op de AcquireTokenInteractive-aanroep gebruikt u de `.WithParentActivityOrWindow(App.RootViewController)` en geeft u de verwijzing naar het object venster dat u gaat gebruiken.

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
In de aanroep voor het verkrijgen van een token:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-5-register-a-url-scheme"></a>Stap 5: een URL-schema registreren
MSAL.NET maakt gebruik van Url's om de Broker aan te roepen en retour neren het Broker-antwoord terug naar uw app. Als u de retour ronding wilt volt ooien, registreert u een URL-schema voor uw app in het `Info.plist`-bestand.

De naam van de `CFBundleURLSchemes` moet `msauth.` bevatten als voor voegsel, gevolgd door uw `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Bijvoorbeeld:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Dit URL-schema wordt onderdeel van de omleidings-URI die wordt gebruikt om uw app op unieke wijze te identificeren wanneer de reactie van de Broker wordt ontvangen.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Stap 6: de Broker-id toevoegen aan de sectie LSApplicationQueriesSchemes
MSAL maakt gebruik van `–canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 zijn de schema's vergrendeld waarvan een toepassing kan zoeken. 

Voeg `msauthv2` toe aan de sectie `LSApplicationQueriesSchemes` van het `Info.plist` bestand.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Stap 7: de omleidings-URI registreren in de toepassings Portal
Als u de Broker gebruikt, wordt er een extra vereiste voor de omleidings-URI toegevoegd. De omleidings-URI _moet_ de volgende indeling hebben:
```csharp
$"msauth.{BundleId}://auth"
```
**Bijvoorbeeld:**
```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
U ziet dat de omleidings-URI overeenkomt met de naam van de `CFBundleURLSchemes` die u in het `Info.plist` bestand hebt opgenomen.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Stap 8: Zorg ervoor dat de omleidings-URI is geregistreerd bij uw app

Deze omleidings-URI moet worden geregistreerd op de portal voor app-registratie (https://portal.azure.com) als een geldige omleidings-URI voor uw toepassing. 

De portal heeft een nieuwe portal voor het registreren van apps waarmee u de brokered antwoord-URI kunt berekenen op basis van de bundel-ID.

1. Kies in de app-registratie **verificatie** en selecteer **de nieuwe ervaring uitproberen**.

   ![De nieuwe app-registratie-ervaring uitproberen](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecteer **een platform toevoegen**.

   ![Een platform toevoegen](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Wanneer de lijst met platformen wordt ondersteund, selecteert u **IOS**.

   ![IOS configureren](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Voer de gewenste bundel-ID in en selecteer vervolgens **configureren**.

   ![Bundel-ID invoeren](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. De omleidings-URI wordt voor u berekend.

   ![Omleidings-URI kopiëren](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered authenticatie voor Android

MSAL.NET ondersteunen op het moment alleen het platform Xamarin. iOS. Het biedt nog geen ondersteuning voor Brokers voor het platform Xamarin. Android.

De systeem eigen bibliotheek van MSAL Android ondersteunt dit al. Zie [brokered auth in Android](brokered-auth.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [universeel Windows-platform-specifieke overwegingen met MSAL.net](msal-net-uwp-considerations.md).
