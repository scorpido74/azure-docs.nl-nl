---
title: Xamarin Android-overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Xamarin Android met de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 54df91d38541fbe17a28c9ae083ae0e7d0c9d88d
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063659"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Xamarin Android-specifieke overwegingen met MSAL.NET
In dit artikel worden specifieke overwegingen beschreven voor het gebruik van Xamarin Android met de micro soft Authentication Library voor .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>De bovenliggende activiteit instellen

Op Xamarin. Android moet u de bovenliggende activiteit zo instellen dat het token wordt teruggedraaid zodra de interactie is opgetreden.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
U kunt dit ook instellen op het PublicClientApplication niveau (in MSAL 4.2 +) via een call back.

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Een aanbeveling is om [hier](https://github.com/jamesmontemagno/CurrentActivityPlugin)de CurrentActivityPlugin te gebruiken.  De PublicClientApplication Builder-code ziet er als volgt uit:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Controleren of het besturings element terugkeert naar MSAL zodra het interactieve deel van de verificatie stroom is beëindigd
Op Android moet u de `OnActivityResult` methode van de `Activity` overschrijven en de methode SetAuthenticationContinuationEventArgs van de klasse AuthenticationContinuationHelper MSAL aanroepen.

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
Deze regel zorgt ervoor dat het besturings element terugkeert naar MSAL zodra het interactieve deel van de verificatie stroom is beëindigd.

## <a name="update-the-android-manifest"></a>Het Android-manifest bijwerken
De `AndroidManifest.xml` moet de volgende waarden bevatten:
```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash"/>
         </intent-filter>
</activity>
```
Vervang de naam van het pakket die u hebt geregistreerd in het Azure Portal voor de `android:host=` waarde. Vervang de sleutel-hash die u hebt geregistreerd in het Azure Portal voor de `android:path=` waarde. De hash van de hand tekening mag **geen** URL-code ring zijn. Zorg ervoor dat er een toonaangevende `/` aan het begin van uw hand tekening-hash.

U kunt ook [de activiteit in code maken](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) en niet hand matig bewerken `AndroidManifest.xml`. Hiervoor moet u een klasse met het kenmerk `Activity` en `IntentFilter` maken. Een klasse die dezelfde waarden van de bovenstaande XML vertegenwoordigt, is als volgt:

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

### <a name="xamarinforms-43x-manifest"></a>XamarinForms 4.3. X-manifest

De code die door XamarinForms 4.3. x wordt gegenereerd, stelt het `package` kenmerk in op `com.companyname.{appName}` in de `AndroidManifest.xml`. Als u de `DataScheme` als `msal{client_id}`gebruikt, is het raadzaam om de waarde te wijzigen zodat deze hetzelfde is als de `MainActivity.cs` naam ruimte.

## <a name="use-the-embedded-web-view-optional"></a>De Inge sloten webweergave gebruiken (optioneel)

MSAL.NET maakt standaard gebruik van de webbrowser systeem, waarmee u SSO met webtoepassingen en andere apps kunt ophalen. In sommige zeldzame gevallen wilt u wellicht opgeven dat u de Inge sloten Webweergave wilt gebruiken. Zie [MSAL.net maakt gebruik van een](msal-net-web-browsers.md) webbrowser en [Android-systeem browser](msal-net-system-browser-android-considerations.md)voor meer informatie.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Problemen oplossen
Als u een nieuwe toepassing Xamarin. Forms maakt en een verwijzing naar het NuGet-pakket MSAL.Net toevoegt, werkt dit alleen.
Als u echter een bestaande Xamarin. Forms-toepassing wilt bijwerken naar MSAL.NET van 1.1.2 of hoger, kunt u problemen ondervinden bij het bouwen.

Als u deze problemen wilt oplossen, moet u het volgende doen:
- Het bestaande MSAL.NET NuGet-pakket bijwerken naar MSAL.NET preview 1.1.2 of hoger
- Controleer of Xamarin. Forms automatisch is bijgewerkt naar versie 2.5.0.122203 (als dit niet het geval is, werkt u deze versie bij)
- Controleer of Xamarin. Android. support. v4 automatisch is bijgewerkt naar versie 25.4.0.2 (als dit niet het geval is, werkt u deze versie bij)
- Alle Xamarin. Android. de ondersteunings pakketten moeten zijn gericht op versie 25.4.0.2
- Schonen/opnieuw samen stellen
- Stel het maximum aantal parallelle project builds in op 1 in Visual Studio (Opties-> projecten en oplossingen-> bouwen en uitvoeren-> Maxi maal paar parallelle projecten)
- Als u vanaf de opdracht regel bouwt, kunt u ook/m verwijderen uit de opdracht als u deze gebruikt.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Fout: de naam ' AuthenticationContinuationHelper ' bestaat niet in de huidige context

Dit komt waarschijnlijk doordat Visual Studio het bestand Android. csproj * niet correct heeft bijgewerkt. Soms bevat de **\<HintPath >** bestandspad onjuist netstandard13 in plaats van **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie en voor beelden vindt u in de para graaf met [specifieke Android-overwegingen](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) van het volgende voor beeld README.MD-bestand:

| Voorbeeld | Platform | Beschrijving |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om MSA en Azure AD te verifiëren via het AADD v 2.0-eind punt en toegang te krijgen tot de Microsoft Graph met het resulterende token. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |
