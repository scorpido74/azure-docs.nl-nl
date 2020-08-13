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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8e19677adf5fe0f64ad9e1c845f516f81ad89512
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166056"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator of Intune-bedrijfsportal gebruiken in Xamarin-toepassingen

Op Android en iOS kunnen brokers als Microsoft Authenticator en de Android-specifieke Microsoft Intune Bedrijfsportal inschakelen:

- **Eenmalige aanmelding (SSO)**: gebruikers hoeven zich niet aan te melden bij elke toepassing.
- **Apparaat-id**: de Broker heeft toegang tot het certificaat van het apparaat. Dit certificaat wordt gemaakt op het apparaat wanneer het is gekoppeld aan de werk plek.
- **Verificatie**van de toepassings-id: wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven. De Broker verifieert de URL.

Als u een van deze functies wilt inschakelen, gebruikt `WithBroker()` u de para meter wanneer u de-methode aanroept `PublicClientApplicationBuilder.CreateApplication` . De `.WithBroker()` para meter is standaard ingesteld op True. 

U kunt ook de instructies in de volgende secties gebruiken om brokered-verificatie in te stellen voor [IOS](#brokered-authentication-for-ios) -toepassingen of [Android](#brokered-authentication-for-android) -toepassingen.

## <a name="brokered-authentication-for-ios"></a>Brokered authenticatie voor iOS

Gebruik de volgende stappen om uw Xamarin. iOS-app in te scha kelen voor communicatie met de app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen
U moet Broker ondersteuning inschakelen voor afzonderlijke exemplaren van `PublicClientApplication` . Ondersteuning is standaard uitgeschakeld. Wanneer u maakt `PublicClientApplication` via `PublicClientApplicationBuilder` , gebruikt u de `WithBroker()` para meter zoals in het volgende voor beeld wordt getoond. De `WithBroker()` para meter is standaard ingesteld op True.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Stap 2: toegang tot sleutel hanger inschakelen

Als u de toegang tot de sleutel keten wilt inschakelen, moet u een toegangs groep voor de sleutel hanger hebben voor uw toepassing. U kunt de `WithIosKeychainSecurityGroup()` API gebruiken om de toegangs groep voor de sleutel hanger in te stellen wanneer u uw toepassing maakt:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Zie [toegang tot sleutel hanger inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)voor meer informatie.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken voor het afhandelen van de call back
Wanneer micro soft Authentication Library voor .NET (MSAL.NET) de Broker aanroept, wordt de Broker teruggebeld naar uw toepassing via de `OpenUrl` methode van de `AppDelegate` klasse. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL.NET terug aan te roepen. Als u deze samen werking wilt inschakelen, werkt u het `AppDelegate.cs` bestand bij om de volgende methode te overschrijven.

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
U moet nog steeds in het `AppDelegate.cs` bestand een object venster instellen. Normaal gesp roken hoeft u voor Xamarin iOS het object venster niet in te stellen. Maar u hebt wel een object venster nodig voor het verzenden en ontvangen van antwoorden van de Broker. 

Het object venster instellen: 
1. Stel in het `AppDelegate.cs` bestand in `App.RootViewController` op een nieuw `UIViewController()` . Deze toewijzing zorgt ervoor dat de aanroep naar de Broker bevat `UIViewController` . Als deze instelling onjuist is toegewezen, kan deze fout optreden:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive`Gebruik `.WithParentActivityOrWindow(App.RootViewController)` en klik vervolgens in de verwijzing naar het object venster dat u wilt gebruiken.

    In `App.cs`:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    In `AppDelegate.cs`:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    In de `AcquireToken` aanroep:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Stap 5: een URL-schema registreren
MSAL.NET maakt gebruik van Url's om de Broker aan te roepen en vervolgens het Broker-antwoord te retour neren aan uw app. Als u de retour ronding wilt volt ooien, registreert u een URL-schema voor uw app in het `Info.plist` bestand.

De `CFBundleURLSchemes` naam moet `msauth.` als voor voegsel bevatten. Volg het voor voegsel met `CFBundleURLName` . 

In het URL-schema is `BundleId` de unieke identificatie van de app: `$"msauth.(BundleId)"` . Als dat zo `BundleId` is `com.yourcompany.xforms` , is het URL-schema `msauth.com.yourcompany.xforms` .

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

MSAL wordt gebruikt `–canOpenURL:` om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 heeft Apple de schema's vergrendeld die een toepassing kan opvragen. 

Voeg toe `msauthv2` aan de `LSApplicationQueriesSchemes` sectie van het `Info.plist` bestand, zoals in het volgende voor beeld:

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

U ziet dat de omleidings-URI overeenkomt met de `CFBundleURLSchemes` naam die u in het bestand hebt opgenomen `Info.plist` .

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Stap 8: Zorg ervoor dat de omleidings-URI is geregistreerd bij uw app

De omleidings-URI moet worden geregistreerd op de [Portal voor app-registratie](https://portal.azure.com) als een geldige omleidings-URI voor uw toepassing. 

De portal voor app-registratie biedt een nieuwe ervaring om u te helpen bij het berekenen van de brokered antwoord-URI van de bundel-ID. 

De omleidings-URI berekenen:

1. Kies in de app-registratie Portal de optie **verificatie**  >  **proberen de nieuwe ervaring**.

   ![De nieuwe app-registratie-ervaring uitproberen](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecteer **een platform toevoegen**.

   ![Een platform toevoegen](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Wanneer de lijst met platformen wordt ondersteund, selecteert u **IOS**.

   ![IOS configureren](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Voer de gewenste bundel-ID in en selecteer vervolgens **configureren**.

   ![Voer de bundel-ID in](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Wanneer u klaar bent met de stappen, wordt de omleidings-URI voor u berekend.

![Omleidings-URI kopiëren](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered authenticatie voor Android

### <a name="step-1-enable-broker-support"></a>Stap 1: ondersteuning voor Broker inschakelen

Broker-ondersteuning is ingeschakeld per PublicClientApplication. Het is standaard uitgeschakeld. Gebruik de `WithBroker()` para meter (standaard ingesteld op True) bij het maken van de `IPublicClientApplication` via `PublicClientApplicationBuilder` .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Stap 2: AppDelegate bijwerken voor het afhandelen van de call back

Wanneer MSAL.NET de Broker aanroept, roept de Broker op zijn beurt terug naar uw toepassing met de methode OnActivityResult (). Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing het resultaat routeren naar MSAL.NET.
Dit kan worden bereikt door het resultaat door te sturen naar de `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` door de methode OnActivityResult () te overschrijven, zoals hieronder wordt weer gegeven

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Deze methode wordt aangeroepen wanneer de Broker-toepassing wordt gestart en wordt gebruikt als een kans om de reactie van de Broker te verwerken en het verificatie proces te volt ooien dat door MSAL.NET is gestart.

### <a name="step-3-set-an-activity"></a>Stap 3: een activiteit instellen

Voor een goede werking van brokered-verificatie moet u een activiteit instellen zodat MSAL de reactie van de broker kan verzenden en ontvangen.

Hiervoor moet u de activiteit (meestal de MainActivity) als `WithParentActivityOrWindow(object parent)` het bovenliggende object opgeven. 

**Bijvoorbeeld:**

In de aanroep voor het verkrijgen van een token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Stap 4: uw RedirectUri in de toepassings Portal registreren

MSAL maakt gebruik van Url's om de Broker aan te roepen en vervolgens terug te keren naar uw app. Voor het volt ooien van deze retour actie moet u een URL-schema voor uw app registreren. Deze omleidings-URI moet worden geregistreerd op de Azure AD-portal voor app-registratie als een geldige omleidings-URI voor uw toepassing.


De omleidings-URI die nodig is voor uw toepassing, is afhankelijk van het certificaat dat is gebruikt voor het ondertekenen van de APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Het laatste deel van de URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` , is de hand tekening waarmee de APK is ondertekend, base64-gecodeerd.
Als u echter tijdens de ontwikkelings fase van uw toepassing met Visual Studio fouten opspoort in uw code zonder de APK te ondertekenen met een specifiek certificaat, wordt de APK door Visual Studio voor u voor fout opsporing ondertekend, waardoor de APK een unieke hand tekening geeft voor de machine waarop deze is gebaseerd. Telkens wanneer u uw app op een andere machine bouwt, moet u de omleidings-URI in de code van de toepassing bijwerken en de registratie van de toepassing in de Azure Portal om te verifiëren met MSAL. 

Tijdens het opsporen van fouten kan er een MSAL-uitzonde ring (of logboek bericht) worden weer gegeven met de mede deling dat de omleidings-URI onjuist is. **Deze uitzonde ring geeft u ook de omleidings-URI die u moet gebruiken** met de huidige machine waarvoor u fouten wilt opsporen. U kunt deze omleidings-URI gebruiken om te blijven ontwikkelen voor de tijd.

Wanneer u klaar bent om uw code te volt ooien, moet u de omleidings-URI in de code bijwerken en de registratie van de toepassing in de Azure Portal om de hand tekening van het certificaat te gebruiken, gaat u de APK ondertekenen met.

In de praktijk houdt dit in dat u een omleidings-URI moet registreren voor elk lid van het team, plus een omleidings-URI voor de door de productie ondertekende versie van de APK.

U kunt deze hand tekening ook zelf berekenen, vergelijkbaar met de manier waarop MSAL dit doet: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

U kunt ook de hand tekening voor het pakket ophalen met behulp van het hulp programma voor de volgende opdrachten:

Voor Windows:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Voor Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [overwegingen voor het gebruik van universeel Windows-platform met MSAL.net](msal-net-uwp-considerations.md).
