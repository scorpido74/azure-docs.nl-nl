---
title: Microsoft Authenticator-of Microsoft Intune-bedrijfs portal gebruiken in Xamarin iOS-en Android-toepassingen | Azure
description: Meer informatie over het migreren van Xamarin iOS-toepassingen kunnen Microsoft Authenticator van de Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) worden gebruikt voor de micro soft-verificatie bibliotheek voor .NET (MSAL.NET)
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
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875638"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator of Microsoft Intune bedrijfs portal gebruiken in Xamarin-toepassingen

Op Android-en iOS-makelaars, zoals Microsoft Authenticator of Microsoft Intune bedrijfs portal inschakelen (alleen Android):

- Eenmalige aanmelding (SSO). Uw gebruikers hoeven zich niet aan te melden bij elke toepassing.
- Apparaat-id. Door toegang te krijgen tot het certificaat van het apparaat, dat is gemaakt op het apparaat toen het is gekoppeld aan de werk plek.
- Verificatie van de toepassings-id. Wanneer een toepassing de Broker aanroept, wordt de omleidings-URL door gegeven en wordt deze door de Broker gecontroleerd.

Om een van deze functies in te scha kelen, moeten ontwikkel aars `WithBroker()` van toepassingen de para `PublicClientApplicationBuilder.CreateApplication` meter gebruiken bij het aanroepen van de-methode. `.WithBroker()`is standaard ingesteld op True. Ontwikkel aars moeten ook de volgende stappen volgen voor [IOS](#brokered-authentication-for-ios) -of [Android](#brokered-authentication-for-android) -toepassingen.

## <a name="brokered-authentication-for-ios"></a>Brokered authenticatie voor iOS

Volg de onderstaande stappen om uw Xamarin. iOS-app in te scha kelen voor communicatie met de app [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Stap 1: Ondersteuning voor Broker inschakelen
Broker-ondersteuning is ingeschakeld per PublicClientApplication. Het is standaard uitgeschakeld. Gebruik de `WithBroker()` para meter (standaard ingesteld op True) bij het maken van de PublicClientApplication via de PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Stap 2: AppDelegate bijwerken voor het afhandelen van de call back
Wanneer MSAL.net de Broker aanroept, roept de Broker op zijn beurt terug naar uw toepassing via de `OpenUrl` methode van de `AppDelegate` -klasse. Omdat MSAL wacht op het antwoord van de Broker, moet uw toepassing samen werken om MSAL.NET terug aan te roepen. Als u deze samen werking wilt inschakelen `AppDelegate.cs` , werkt u het bestand bij om de onderstaande methode te overschrijven.

```CSharp
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

Deze methode wordt aangeroepen telkens wanneer de toepassing wordt gestart en wordt gebruikt als een kans om de reactie van de Broker te verwerken en het verificatie proces te volt ooien dat door MSAL.NET wordt gestart.

### <a name="step-3-set-a-uiviewcontroller"></a>Stap 3: Een UIViewController instellen ()
`AppDelegate.cs`U moet nog steeds een object venster instellen. Normaal gesp roken hoeft u met Xamarin iOS het object venster niet in te stellen, maar voor het verzenden en ontvangen van antwoorden van de Broker, hebt u een object venster nodig. 

Hiervoor moet u twee dingen doen. 
1) Stel `AppDelegate.cs`in de `App.RootViewController` in op een nieuw `UIViewController()`. Deze toewijzing zorgt ervoor dat er een UIViewController is met de aanroep naar de Broker. Als deze niet correct is ingesteld, wordt deze fout mogelijk weer geven:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Gebruik in de AcquireTokenInteractive-aanroep de `.WithParentActivityOrWindow(App.RootViewController)` en geef deze door in de verwijzing naar het object venster dat u gaat gebruiken.

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

### <a name="step-4-register-a-url-scheme"></a>Stap 4: Een URL-schema registreren
MSAL.NET maakt gebruik van Url's om de Broker aan te roepen en retour neren het Broker-antwoord terug naar uw app. Als u de retour ronding wilt volt ooien, moet u een URL-schema voor uw `Info.plist` app registreren in het bestand.

De `CFBundleURLSchemes` naam moet als `msauth.` voor voegsel, gevolgd door uw `CFBundleURLName`worden toegevoegd.

`$"msauth.(BundleId)"`

**Bijvoorbeeld:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Dit maakt deel uit van de RedirectUri die wordt gebruikt voor het uniek identificeren van uw app bij het ontvangen van het antwoord van de Broker.

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

### <a name="step-5-lsapplicationqueriesschemes"></a>Stap 5: LSApplicationQueriesSchemes
MSAL wordt `–canOpenURL:` gebruikt om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 zijn de schema's vergrendeld waarvan een toepassing kan zoeken. 

**Toevoegen** naar de `LSApplicationQueriesSchemes` sectie van het `Info.plist` bestand. **`msauthv2`**

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>Stap 6: Uw RedirectUri registreren in de toepassings Portal
Als u de Broker gebruikt, wordt er een extra vereiste op uw redirectUri. De redirectUri _**moet**_ de volgende indeling hebben:
```CSharp
$"msauth.{BundleId}://auth"
```
**Bijvoorbeeld:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**U ziet dat de RedirectUri overeenkomt `CFBundleURLSchemes` met de naam die u `Info.plist` in het bestand hebt opgenomen.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Stap 7: Zorg ervoor dat de omleidings-URI is geregistreerd bij uw app

Deze omleidings-URI moet worden geregistreerd op de portal voor https://portal.azure.com) app-registratie (als een geldige omleidings-URI voor uw toepassing. 

De portal heeft een nieuwe portal voor het registreren van apps waarmee u de brokered antwoord-URI kunt berekenen op basis van de bundel-ID:

1. In de app-registratie kiest u **verificatie** en selecteert u **de nieuwe ervaring**.
   ![De nieuwe app-registratie-ervaring uitproberen](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Selecteer **platform toevoegen**.
   ![Een platform toevoegen](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Wanneer de lijst met platformen wordt ondersteund, selecteert u **IOS**.
   ![IOS configureren](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Voer de gewenste bundel-ID in en druk op **registreren**.
   ![Bundel-ID invoeren](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. De omleidings-URI wordt voor u berekend.
   ![Omleidings-URI kopiëren](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Brokered authenticatie voor Android

De Broker-ondersteuning is niet beschikbaar voor Android

## <a name="next-steps"></a>Volgende stappen

[Universeel Windows-platform-specifieke overwegingen met MSAL.NET](msal-net-uwp-considerations.md)