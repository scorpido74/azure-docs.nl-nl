---
title: Brokers gebruiken met Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van Xamarin iOS-toepassingen die Microsoft Authenticator en micro soft Authentication Library voor .NET (MSAL.NET) kunnen gebruiken. Meer informatie over het migreren van Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) naar micro soft Authentication Library voor .NET (MSAL.NET).
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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377453"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator of Intune-bedrijfsportal gebruiken in Xamarin-toepassingen

Op Android en iOS kunnen brokers als Microsoft Authenticator en de Android-specifieke Microsoft Intune Bedrijfsportal inschakelen:

- **Eenmalige aanmelding (SSO)** : gebruikers hoeven zich niet aan te melden bij elke toepassing.
- **Apparaat-id**: de Broker heeft toegang tot het certificaat van het apparaat. Dit certificaat wordt gemaakt op het apparaat wanneer het is gekoppeld aan de werk plek.
- **Verificatie**van de toepassings-id: wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven. De Broker verifieert de URL.

Als u een van deze functies wilt inschakelen, gebruikt u de para meter `WithBroker()` wanneer u de `PublicClientApplicationBuilder.CreateApplication`-methode aanroept. De para meter `.WithBroker()` is standaard ingesteld op True. 

U kunt ook de instructies in de volgende secties gebruiken om brokered-verificatie in te stellen voor [IOS](#brokered-authentication-for-ios) -toepassingen of [Android](#brokered-authentication-for-android) -toepassingen.

## <a name="brokered-authentication-for-ios"></a>Brokered authenticatie voor iOS

Gebruik de volgende stappen om uw Xamarin. iOS-app in te scha kelen voor communicatie met de app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen
U moet Broker ondersteuning inschakelen voor afzonderlijke exemplaren van `PublicClientApplication`. Ondersteuning is standaard uitgeschakeld. Wanneer u `PublicClientApplication` via `PublicClientApplicationBuilder`maakt, gebruikt u de para meter `WithBroker()`, zoals in het volgende voor beeld wordt weer gegeven. De para meter `WithBroker()` is standaard ingesteld op True.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Stap 2: toegang tot sleutel hanger inschakelen

Als u de toegang tot de sleutel keten wilt inschakelen, moet u een toegangs groep voor de sleutel hanger hebben voor uw toepassing. U kunt de `WithIosKeychainSecurityGroup()`-API gebruiken om de toegangs groep voor de sleutel hanger in te stellen wanneer u uw toepassing maakt:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Zie [toegang tot sleutel hanger inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)voor meer informatie.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken voor het afhandelen van de call back
Wanneer micro soft Authentication Library voor .NET (MSAL.NET) de Broker aanroept, wordt de Broker teruggebeld naar uw toepassing via de `OpenUrl` methode van de `AppDelegate`-klasse. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL.NET terug aan te roepen. Als u deze samen werking wilt inschakelen, werkt u het `AppDelegate.cs` bestand bij om de volgende methode te overschrijven.

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

Deze methode wordt aangeroepen telkens wanneer de toepassing wordt gestart. Het wordt gebruikt als een kans om de reactie van de Broker te verwerken en het verificatie proces te volt ooien dat MSAL.NET is gestart.

### <a name="step-4-set-uiviewcontroller"></a>Stap 4: set UIViewController ()
U moet nog steeds in het `AppDelegate.cs`-bestand een object venster instellen. Normaal gesp roken hoeft u voor Xamarin iOS het object venster niet in te stellen. Maar u hebt wel een object venster nodig voor het verzenden en ontvangen van antwoorden van de Broker. 

Het object venster instellen: 
1. Stel in het `AppDelegate.cs`-bestand `App.RootViewController` in op een nieuwe `UIViewController()`. Deze toewijzing zorgt ervoor dat de aanroep naar de Broker `UIViewController`bevat. Als deze instelling onjuist is toegewezen, kan deze fout optreden:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Gebruik `.WithParentActivityOrWindow(App.RootViewController)` in het `AcquireTokenInteractive`-gesprek en geef de verwijzing naar het object venster dat u gaat gebruiken.

    In `App.cs`:
    
    ```csharp
       public static object RootViewController { get; set; }
    ```
    
    In `AppDelegate.cs`:
    
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    
    In het `AcquireToken`-gesprek:
    
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Stap 5: een URL-schema registreren
MSAL.NET maakt gebruik van Url's om de Broker aan te roepen en vervolgens het Broker-antwoord te retour neren aan uw app. Als u de retour ronding wilt volt ooien, registreert u een URL-schema voor uw app in het `Info.plist`-bestand.

De naam van de `CFBundleURLSchemes` moet `msauth.` als voor voegsel bevatten. Volg het voor voegsel met `CFBundleURLName`. 

In het URL-schema `BundleId` unieke identificatie van de app: `$"msauth.(BundleId)"`. Als `BundleId` `com.yourcompany.xforms`is, wordt het URL-schema `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Dit URL-schema wordt onderdeel van de omleidings-URI waarmee uw app uniek wordt geïdentificeerd wanneer de reactie van de Broker wordt ontvangen.

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
MSAL maakt gebruik van `–canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 heeft Apple de schema's vergrendeld die een toepassing kan opvragen. 

Voeg `msauthv2` toe aan de sectie `LSApplicationQueriesSchemes` van het bestand `Info.plist`, zoals in het volgende voor beeld:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Stap 7: de omleidings-URI registreren in de toepassings Portal
Wanneer u de Broker gebruikt, heeft de omleidings-URI een extra vereiste. De omleidings-URI _moet_ de volgende indeling hebben:
```csharp
$"msauth.{BundleId}://auth"
```

Hier volgt een voorbeeld: 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
U ziet dat de omleidings-URI overeenkomt met de `CFBundleURLSchemes` naam die u hebt opgenomen in het `Info.plist` bestand.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Stap 8: Zorg ervoor dat de omleidings-URI is geregistreerd bij uw app

De omleidings-URI moet worden geregistreerd op de [Portal voor app-registratie](https://portal.azure.com) als een geldige omleidings-URI voor uw toepassing. 

De portal voor app-registratie biedt een nieuwe ervaring om u te helpen bij het berekenen van de brokered antwoord-URI van de bundel-ID. 

De omleidings-URI berekenen:

1. Kies in de app-registratie Portal de optie **verificatie** > **de nieuwe ervaring uit te proberen**.

   ![De nieuwe app-registratie-ervaring uitproberen](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecteer **een platform toevoegen**.

   ![Een platform toevoegen](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Wanneer de lijst met platformen wordt ondersteund, selecteert u **IOS**.

   ![IOS configureren](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Voer de gewenste bundel-ID in en selecteer vervolgens **configureren**.

   ![Voer de bundel-ID in](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Wanneer u de stappen hebt voltooid, wordt de omleidings-URI voor u berekend.

![Omleidings-URI kopiëren](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered authenticatie voor Android

MSAL.NET ondersteunt alleen het platform Xamarin. iOS. Het biedt nog geen ondersteuning voor Brokers voor het platform Xamarin. Android.

De systeem eigen bibliotheek van MSAL Android ondersteunt al brokered-verificatie. Zie [brokered Authentication in Android](brokered-auth.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [overwegingen voor het gebruik van universeel Windows-platform met MSAL.net](msal-net-uwp-considerations.md).
