---
title: Xamarin Android overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Xamarin Android met Microsoft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132503"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Overwegingen voor het gebruik van Xamarin Android met MSAL.NET
In dit artikel wordt beschreven waar u rekening mee moet houden wanneer u Xamarin Android gebruikt met Microsoft Authentication Library voor .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>De bovenliggende activiteit instellen

Stel op Xamarin Android de bovenliggende activiteit in, zodat het token terugkeert na de interactie. Hier is een codevoorbeeld:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL 4.2 en hoger u deze functionaliteit `PublicClientApplication`ook instellen op het niveau van. Gebruik hiervoor een callback:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Als u [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)gebruikt, ziet uw `PublicClientApplication` bouwercode er als volgt uit.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Ervoor zorgen dat de controle terugkeert naar MSAL 
Wanneer het interactieve gedeelte van de verificatiestroom eindigt, moet u ervoor zorgen dat de besturing teruggaat naar MSAL. Op Android, `OnActivityResult` overschrijven `Activity`van de methode van . Roep dan `SetAuthenticationContinuationEventArgs` de `AuthenticationContinuationHelper` methode van de klasse MSAL aan. 

Hier volgt een voorbeeld:

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```

Deze regel zorgt ervoor dat het besturingselement terugkeert naar MSAL aan het einde van het interactieve gedeelte van de verificatiestroom.

## <a name="update-the-android-manifest"></a>Update het Android-manifest
Het *bestand AndroidManifest.xml* moet de volgende waarden bevatten:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Vervang de pakketnaam die u hebt `android:host=` geregistreerd in de Azure-portal voor de waarde. Vervang de sleutelhash die u hebt `android:path=` geregistreerd in de Azure-portal voor de waarde. De handtekeninghash mag *niet* worden gecodeerd met url's. Zorg ervoor dat er`/`een slash (forward slash) wordt weergegeven aan het begin van uw handtekeninghash.

U ook [de activiteit in code maken in](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) plaats van *AndroidManifest.xml*handmatig te bewerken. Als u de activiteit in code wilt `Activity` maken, `IntentFilter` maakt u eerst een klasse met het kenmerk en het kenmerk. 

Hier ziet u een voorbeeld van een klasse die de waarden van het XML-bestand vertegenwoordigt:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Formulieren 4.3.X manifest

Xamarin.Forms 4.3.x genereert code `package` die `com.companyname.{appName}` het kenmerk instelt op in *AndroidManifest.xml*. Als u `DataScheme` `msal{client_id}`als , dan wilt u misschien de waarde `MainActivity.cs` te wijzigen om de waarde van de naamruimte wedstrijd.

## <a name="use-the-embedded-web-view-optional"></a>De ingesloten webweergave gebruiken (optioneel)

Standaard gebruikt MSAL.NET de webbrowser van het systeem. Met deze browser u eenmalige aanmelding (SSO) krijgen met behulp van webapplicaties en andere apps. In sommige zeldzame gevallen wilt u misschien dat uw systeem een ingesloten webweergave gebruikt. 

In dit codevoorbeeld ziet u hoe u een ingesloten webweergave instelt:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Zie [Webbrowsers gebruiken voor MSAL.NET](msal-net-web-browsers.md) en [Xamarin Android-systeembrowser overwegingen](msal-net-system-browser-android-considerations.md)voor meer informatie.


## <a name="troubleshoot"></a>Problemen oplossen
U een nieuwe Xamarin.Forms-toepassing maken en een verwijzing toevoegen naar het MSAL.NET NuGet-pakket.
Maar u build problemen hebben als u een bestaande Xamarin.Forms-toepassing upgradet naar MSAL.NET voorbeeld 1.1.2 of hoger.

Ga als instellen om buildproblemen op te lossen:

- Werk het bestaande MSAL.NET NuGet-pakket bij aan MSAL.NET preview 1.1.2 of hoger.
- Controleer of Xamarin.Forms automatisch is bijgewerkt naar versie 2.5.0.122203. Werk Xamarin.Forms indien nodig bij naar deze versie.
- Controleer of Xamarin.Android.Support.v4 automatisch is bijgewerkt naar versie 25.4.0.2. Update indien nodig naar versie 25.4.0.2.
- Zorg voor alle Xamarin.Android.Support-pakketten richten op versie 25.4.0.2.
- Maak de toepassing schoon of herbouw deze opnieuw.
- Probeer in Visual Studio het maximum aantal parallelle projectbuilds in te stellen op 1. Selecteer hiervoor **Optiesprojecten** > **en oplossingen** > **voor het bouwen en uitvoeren** > **van maximaal aantal parallelle projecten.**
- Als u bouwt vanaf de opdrachtregel `/m`en de opdracht wordt gebruikt, probeert u dit element uit de opdracht te verwijderen.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Fout: De naam AuthenticationContinuationHelper bestaat niet in de huidige context

Als een fout `AuthenticationContinuationHelper` aangeeft dat dit niet in de huidige context bestaat, heeft Visual Studio het Android.csproj*-bestand mogelijk onjuist bijgewerkt. Soms bevat het * \<HintPath->* bestandspad ten onrechte *netstandard13* in plaats van *monoandroid90*.

In dit voorbeeld vindt u een correct bestandspad:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie het voorbeeld van een [mobiele Xamarin-toepassing die gebruikmaakt van het Microsoft-identiteitsplatform.](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) In de volgende tabel worden de relevante informatie in het README-bestand samengevat.

| Voorbeeld | Platform | Beschrijving |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Een eenvoudige App Xamarin.Forms die laat zien hoe u MSAL gebruikt om persoonlijke accounts van Microsoft en Azure AD te verifiëren via het Azure AD 2.0-eindpunt. De app laat ook zien hoe u toegang krijgt tot Microsoft Graph en toont het resulterende token. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |
