---
title: Gebruik brokers met Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van Xamarin iOS-toepassingen die Microsoft Authenticator en Microsoft Authentication Library kunnen gebruiken voor .NET (MSAL.NET). Lees ook hoe u migreren van Azure AD-verificatiebibliotheek voor .NET (ADAL.NET) naar Microsoft-verificatiebibliotheek voor .NET (MSAL.NET).
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
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262787"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator of Intune Company Portal gebruiken voor Xamarin-toepassingen

Op Android en iOS maken makelaars als Microsoft Authenticator en de Android-specifieke Microsoft Intune Company Portal het inschakelen:

- **Single sign-on (SSO)**: Gebruikers hoeven zich niet aan te melden bij elke toepassing.
- **Apparaatidentificatie:** de makelaar heeft toegang tot het apparaatcertificaat. Dit certificaat wordt gemaakt op het apparaat wanneer het is verbonden met de werkplek.
- **Verificatie van toepassingsidentificatie:** Wanneer een toepassing de makelaar aanroept, passeert deze de omleidings-URL. De makelaar verifieert de URL.

Als u een van deze `WithBroker()` functies wilt `PublicClientApplicationBuilder.CreateApplication` inschakelen, gebruikt u de parameter wanneer u de methode aanroept. De `.WithBroker()` parameter is standaard ingesteld op true. 

Gebruik ook de instructies in de volgende secties om gebrokereerde verificatie voor [iOS-toepassingen](#brokered-authentication-for-ios) of [Android-toepassingen](#brokered-authentication-for-android) in te stellen.

## <a name="brokered-authentication-for-ios"></a>Gebrokereerde verificatie voor iOS

Gebruik de volgende stappen om uw Xamarin.iOS-app in staat te stellen te praten met de [Microsoft Authenticator-app.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Stap 1: Ondersteuning voor makelaars inschakelen
U moet ondersteuning van de `PublicClientApplication`makelaar inschakelen voor afzonderlijke exemplaren van . Ondersteuning is standaard uitgeschakeld. Wanneer u `PublicClientApplication` `PublicClientApplicationBuilder`doormaakt, `WithBroker()` gebruikt u de parameter zoals in het volgende voorbeeld wordt weergegeven. De `WithBroker()` parameter is standaard ingesteld op true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Stap 2: Toegang tot sleutelhangers inschakelen

Om toegang tot sleutelhangers mogelijk te maken, moet u een sleutelhangertoegangsgroep voor uw toepassing hebben. U `WithIosKeychainSecurityGroup()` de API gebruiken om uw sleutelhangertoegangsgroep in te stellen wanneer u uw toepassing maakt:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Zie [Toegang tot sleutelhanger inschakelen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)voor meer informatie .

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Stap 3: AppDelegate bijwerken om de terugbelfunctie af te handelen
Wanneer Microsoft Authentication Library voor .NET (MSAL.NET) de broker aanroept, belt de broker terug naar uw toepassing via de `OpenUrl` methode van de `AppDelegate` klasse. Omdat MSAL wacht op de reactie van de makelaar, moet uw aanvraag samenwerken om MSAL.NET terug te bellen. Als u deze samenwerking `AppDelegate.cs` wilt inschakelen, werkt u het bestand bij om de volgende methode te overschrijven.

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

Deze methode wordt elke keer dat de toepassing wordt gestart, aangeroepen. Het wordt gebruikt als een kans om de reactie van de makelaar te verwerken en het verificatieproces te voltooien dat MSAL.NET gestart.

### <a name="step-4-set-uiviewcontroller"></a>Stap 4: UiViewController instellen()
Nog steeds `AppDelegate.cs` in het bestand moet u een objectvenster instellen. Normaal gesproken hoeft u voor Xamarin iOS het objectvenster niet in te stellen. Maar je hebt wel een objectvenster nodig om reacties van de makelaar te verzenden en te ontvangen. 

Ga als u het objectvenster instelt: 
1. Stel `AppDelegate.cs` in het `App.RootViewController` bestand `UIViewController()`een nieuwe . Deze opdracht zorgt ervoor dat `UIViewController`de oproep naar de makelaar omvat . Als deze instelling onjuist is toegewezen, u deze fout krijgen:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Gebruik `AcquireTokenInteractive` en geef `.WithParentActivityOrWindow(App.RootViewController)` vervolgens de verwijzing naar het objectvenster dat u gebruikt in de aanroep.

    In `App.cs`:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    In `AppDelegate.cs`:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    In `AcquireToken` de oproep:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Stap 5: Een URL-schema registreren
MSAL.NET gebruikt URL's om de makelaar aan te roepen en vervolgens het antwoord van de makelaar terug te sturen naar uw app. Als u de heen- en terugreis `Info.plist` wilt voltooien, registreert u een URL-schema voor uw app in het bestand.

De `CFBundleURLSchemes` naam `msauth.` moet als voorvoegsel worden vermeld. Volg het voorvoegsel met `CFBundleURLName`. 

In het URL-schema wordt de app `BundleId` op unieke wijze geïdentificeerd: `$"msauth.(BundleId)"`. Dus `BundleId` als `com.yourcompany.xforms`dat zo is, dan is `msauth.com.yourcompany.xforms`de URL-regeling .

> [!NOTE]
> Dit URL-schema wordt onderdeel van de omleiding URI die uw app op unieke wijze identificeert wanneer deze het antwoord van de broker ontvangt.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Stap 6: De broker-id toevoegen aan de sectie LSApplicationQueriesSchemes

MSAL `–canOpenURL:` gebruikt om te controleren of de makelaar is geïnstalleerd op het apparaat. In iOS 9 heeft Apple de schema's vergrendeld waarvoor een toepassing kan zoeken. 

Toevoegen `msauthv2` aan `LSApplicationQueriesSchemes` het `Info.plist` gedeelte van het bestand, zoals in het volgende voorbeeld:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Stap 7: Registreer uw omleiding URI in de applicatie portal

Wanneer u de makelaar gebruikt, heeft uw omleiding URI een extra vereiste. De omleidings-URI _moet_ de volgende indeling hebben:

```csharp
$"msauth.{BundleId}://auth"
```

Hier volgt een voorbeeld:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Merk op dat de `CFBundleURLSchemes` omleiding URI overeenkomt `Info.plist` met de naam die u in het bestand hebt opgenomen.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Stap 8: Zorg ervoor dat de omleiding URI is geregistreerd bij uw app

De omleiding URI moet worden geregistreerd op de [app registratie portal](https://portal.azure.com) als een geldige omleiding URI voor uw aanvraag. 

De app registratie portal biedt een nieuwe ervaring om u te helpen berekenen van de brokered antwoord URI van de bundel-ID. 

Ga als u de omleidings-URI berekenen:

1. Kies in de app-registratieportal **Verificatie** > **Probeer de nieuwe ervaring uit.**

   ![Probeer de nieuwe app-registratie-ervaring uit](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecteer **Een platform toevoegen**.

   ![Een platform toevoegen](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Wanneer de lijst met platforms wordt ondersteund, selecteert u **iOS**.

   ![IOS configureren](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Voer de gevraagde bundel-id in en selecteer **Configureren**.

   ![Voer de bundel-id in](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Wanneer u klaar bent met de stappen, wordt de omleidinguri voor u berekend.

![Omleiding URI kopiëren](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered authentication voor Android

### <a name="step-1-enable-broker-support"></a>Stap 1: Ondersteuning voor makelaars inschakelen

Broker-ondersteuning is ingeschakeld op basis van een openbare clientapplicatie. Het is standaard uitgeschakeld. Gebruik `WithBroker()` de parameter (standaard ingesteld op `IPublicClientApplication` true) `PublicClientApplicationBuilder`bij het maken van de door de .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Stap 2: AppDelegate bijwerken om de terugbelfunctie af te handelen

Wanneer MSAL.NET de makelaar belt, belt de broker op zijn beurt terug naar uw toepassing met de methode OnActivityResult(). Aangezien MSAL zal wachten op het antwoord van de makelaar, uw aanvraag moet het resultaat route naar MSAL.NET.
Dit kan worden bereikt door `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` het resultaat te routeren naar de methode OnActivityResult(), zoals hieronder wordt weergegeven

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Deze methode wordt aangeroepen elke keer dat de broker-toepassing wordt gestart en wordt gebruikt als een kans om de reactie van de makelaar te verwerken en het verificatieproces te voltooien dat door MSAL.NET is gestart.

### <a name="step-3-set-an-activity"></a>Stap 3: Een activiteit instellen

Om gebrokereerde verificatie te laten werken, moet u een activiteit instellen, zodat MSAL het antwoord van de makelaar kan verzenden en ontvangen.

Om dit te doen, moet u de activiteit (meestal `WithParentActivityOrWindow(object parent)` de MainActivity) aan het als bovenliggende object verstrekken. 

**Bijvoorbeeld:**

In de aanroep Token verwerven:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Stap 4: Registreer uw RedirectUri in de applicatieportal

MSAL gebruikt URL's om de makelaar aan te roepen en vervolgens terug te keren naar uw app. Om die heen- en terugreis te voltooien, moet u een URL-schema voor uw app registreren. Deze OmleidingsURI moet worden geregistreerd op de Azure AD-app-registratieportal als een geldige omleidingsuri voor uw toepassing.


De omleiding uri die nodig is voor uw aanvraag is afhankelijk van het certificaat dat wordt gebruikt om de APK te ondertekenen.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Het laatste deel van `hgbUYHVBYUTvuvT&Y6tr554365466=`de URI, , is de handtekening waarmee de APK is ondertekend, base64 gecodeerd.
Tijdens de ontwikkelingsfase van uw toepassing met Visual Studio, als u uw code debugt zonder de apk te ondertekenen met een specifiek certificaat, zal Visual Studio de apk voor u ondertekenen voor foutopsporingsdoeleinden, waardoor de APK een unieke handtekening krijgt voor de machine waarop hij is gebouwd. Dus elke keer dat u uw app op een andere machine bouwt, moet u de omleidingsURI bijwerken in de code van de toepassing en de registratie van de toepassing in de Azure-portal om te verifiëren met MSAL. 

Tijdens het debuggen u een MSAL-uitzondering (of logboekbericht) tegenkomen waarin staat dat de verstrekte omleidings-URI onjuist is. **Deze uitzondering zal u ook voorzien van de omleiding URI die u moet gebruiken** met de huidige machine die u debugging op. U deze omleiding URI gebruiken om te blijven ontwikkelen voor het moment.

Zodra u klaar bent om uw code af te ronden, moet u de omleidinguri in de code en op de registratie van de toepassing in de Azure-portal bijwerken om de handtekening te gebruiken van het certificaat waarmee u de APK ondertekent.

In de praktijk betekent dit dat u een omleiding URI voor elk lid van het team moet registreren, plus een omleiding URI voor de productie ondertekende versie van de APK.

U deze handtekening ook zelf berekenen, vergelijkbaar met hoe MSAL het doet: 

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

Je hebt ook de mogelijkheid om de handtekening voor je pakket te verkrijgen met behulp van de keytool met de volgende opdrachten:

Voor Windows:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Voor Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [overwegingen voor het gebruik van universal Windows Platform met MSAL.NET](msal-net-uwp-considerations.md).
