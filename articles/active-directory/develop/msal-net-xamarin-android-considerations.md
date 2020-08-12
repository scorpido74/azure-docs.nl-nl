---
title: Xamarin Android-overwegingen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over overwegingen voor het gebruik van Xamarin Android met micro soft Authentication Library voor .NET (MSAL.NET).
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
ms.openlocfilehash: d244bbbe96bcea45da5c0860e4af52409123fb7f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118685"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Overwegingen voor het gebruik van Xamarin Android met MSAL.NET
In dit artikel wordt beschreven wat u moet overwegen wanneer u Xamarin Android gebruikt met micro soft Authentication Library voor .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>De bovenliggende activiteit instellen

Stel op Xamarin Android de bovenliggende activiteit zo in dat het token wordt geretourneerd na de interactie. Hier volgt een code voorbeeld:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL 4,2 en hoger kunt u deze functie ook instellen op het niveau van `PublicClientApplication` . Gebruik hiervoor een call back:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Als u [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)gebruikt, ziet uw `PublicClientApplication` Builder-code eruit zoals in het volgende voor beeld.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Controleer of het besturings element terugkeert naar MSAL 
Wanneer het interactieve gedeelte van de verificatie stroom eindigt, zorgt u ervoor dat het besturings element weer teruggaat naar MSAL. Negeer de `OnActivityResult` methode van op Android `Activity` . Roep vervolgens de `SetAuthenticationContinuationEventArgs` methode van de `AuthenticationContinuationHelper` klasse MSAL aan. 

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

Deze regel zorgt ervoor dat het besturings element terugkeert naar MSAL aan het einde van het interactieve deel van de verificatie stroom.

## <a name="update-the-android-manifest"></a>Het Android-manifest bijwerken
Het *AndroidManifest.xml* bestand moet de volgende waarden bevatten:

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

Vervang de waarde door de naam van het pakket dat u hebt geregistreerd in het Azure Portal `android:host=` . Vervang de sleutel-hash die u hebt geregistreerd in het Azure Portal voor de `android:path=` waarde. De hash van de hand tekening mag *geen* URL-code ring zijn. Zorg ervoor dat er een voorloop back slash ( `/` ) aan het begin van uw hand tekening-hash wordt weer gegeven.

U kunt ook [de activiteit in code maken in](/xamarin/android/platform/android-manifest#the-basics) plaats van *AndroidManifest.xml*hand matig te bewerken. Als u de activiteit in code wilt maken, maakt u eerst een klasse die het `Activity` kenmerk en het `IntentFilter` kenmerk bevat. 

Hier volgt een voor beeld van een klasse die de waarden van het XML-bestand vertegenwoordigt:

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

### <a name="xamarinforms-43x-manifest"></a>Xamarin. Forms 4.3. X-manifest

Xamarin. Forms 4.3. x genereert code waarmee het `package` kenmerk wordt ingesteld `com.companyname.{appName}` in *AndroidManifest.xml*. Als u `DataScheme` als gebruikt `msal{client_id}` , wilt u mogelijk de waarde wijzigen zodat deze overeenkomt met de waarde van de `MainActivity.cs` naam ruimte.

## <a name="use-the-embedded-web-view-optional"></a>De Inge sloten webweergave gebruiken (optioneel)

MSAL.NET maakt standaard gebruik van de webbrowser van het systeem. Met deze browser kunt u eenmalige aanmelding (SSO) ophalen met behulp van webtoepassingen en andere apps. In sommige zeldzame gevallen kan het nodig zijn dat uw systeem gebruikmaakt van een Inge sloten webweergave. 

Dit code voorbeeld laat zien hoe u een Inge sloten webweergave instelt:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Zie voor meer informatie [webbrowsers gebruiken voor MSAL.net](msal-net-web-browsers.md) en [Xamarin Android-systeem browser overwegingen](msal-net-system-browser-android-considerations.md).


## <a name="troubleshoot"></a>Problemen oplossen
U kunt een nieuwe toepassing Xamarin. Forms maken en een verwijzing naar het MSAL.NET NuGet-pakket toevoegen.
Maar mogelijk hebt u problemen met het samen stellen als u een bestaande Xamarin. Forms-toepassing bijwerkt naar MSAL.NET voor beeld 1.1.2 of hoger.

Problemen bij het bouwen oplossen:

- Werk het bestaande MSAL.NET NuGet-pakket bij naar MSAL.NET preview 1.1.2 of hoger.
- Controleer of Xamarin. Forms automatisch is bijgewerkt naar versie 2.5.0.122203. Werk indien nodig Xamarin. Forms bij naar deze versie.
- Controleer of Xamarin. Android. support. v4 automatisch is bijgewerkt naar versie 25.4.0.2. Indien nodig werkt u bij naar versie 25.4.0.2.
- Zorg ervoor dat alle Xamarin. Android. support packages doel versie 25.4.0.2 hebben.
- De toepassing opschonen of opnieuw bouwen.
- In Visual Studio kunt u het maximum aantal parallelle project builds instellen op 1. Hiertoe selecteert u **Opties**  >  **projecten en oplossingen**  >  **bouwen en voert**u het  >  **maximum aantal parallelle projecten-builds**uit.
- Als u bouwt vanaf de opdracht regel en de opdracht wordt gebruikt `/m` , verwijdert u dit element uit de opdracht.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Fout: de naam AuthenticationContinuationHelper bestaat niet in de huidige context

Als een fout aangeeft dat `AuthenticationContinuationHelper` niet aanwezig is in de huidige context, heeft Visual Studio mogelijk het bestand Android. csproj * onjuist bijgewerkt. Soms *\<HintPath>* bevat het bestandspad onjuist *netstandard13* in plaats van *monoandroid90*.

Dit voor beeld bevat een juist bestandspad:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie het voor beeld van een [Xamarin Mobile-toepassing die gebruikmaakt van micro soft Identity platform](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). De volgende tabel bevat een overzicht van de relevante gegevens in het Leesmij-bestand.

| Voorbeeld | Platform | Beschrijving |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Een eenvoudige Xamarin. Forms-app die laat zien hoe u MSAL kunt gebruiken om micro soft-persoonlijke accounts en Azure AD te verifiëren via het Azure AD 2,0-eind punt. De app laat ook zien hoe u toegang krijgt tot Microsoft Graph en hoe het resulterende token wordt weer gegeven. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |