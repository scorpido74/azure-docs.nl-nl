---
title: 'Quickstart: Speech herkennen, C# (Xamarin)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u een cross-platform C# Xamarin-toepassing voor Windows UWP, Android en IOS met behulp van de SDK van Cognitive Services speech. U transcribeert spraak naar tekst in realtime van de microfoon van uw apparaat of Simulator. De toepassing is gebouwd met het Speech SDK NuGet-pakket en micro soft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: 02031e8e2800329134eaf34f5f99c93a20b99aac
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804053"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>Quickstart: Spraak herkennen met een platformoverschrijdende Xamarin-app met behulp van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak naar tekst](quickstart-csharp-uwp.md), [tekst naar spraak](quickstart-text-to-speech-csharp-uwp.md) -en [spraak omzetting](quickstart-translate-speech-uwp.md).

In dit artikel gaat u een platformoverschrijdende C# toepassing ontwikkelen met behulp van Xamarin voor universeel Windows-platform (UWP), Android en IOS met behulp van de [Speech SDK](speech-sdk.md). Het programma transcribeert spraak naar tekst in realtime van de microfoon van uw apparaat. De toepassing is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en micro soft Visual Studio 2019 (alle edities).

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](get-started.md).
* Een Windows-PC met Windows 10 najaar makers update (10,0; Build 16299) of hoger en met een werkende microfoon.
* [Xamarin-installatie naar Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Xamarin Android-installatie in Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Xamarin IOS-installatie in Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Android-doel: 
   * Een Android-apparaat (ARM32/64, x86; API 23: Android 6,0 Marshmallow of hoger) [ingeschakeld voor ontwikkeling](https://developer.android.com/studio/debug/dev-options) met een werkende microfoon.
* Voor het doel iOS: een iOS-apparaat (ARM64) of een iOS-Simulator (x64) die is [ingeschakeld voor ontwikkeling](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) met een werkende microfoon.
* Voor ondersteuning voor Windows ARM64 bouwen installeert u de [optionele build tools en de Windows 10 SDK voor arm/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Voorbeeld code toevoegen voor het project common `helloworld`

Het algemene `helloworld`-project bevat platform onafhankelijke implementaties voor uw platform voor meerdere platforms.
Voeg nu de XAML-code toe die de gebruikers interface van de toepassing definieert en voeg C# de code achter de implementatie toe.

1. Open in **Solution Explorer**onder het gemeen schappelijke `helloworld`-project `MainPage.xaml`.

1. In de XAML-weer gave van de ontwerp functie plaatst u het volgende XAML-fragment in de **grid** -tag (tussen `<StackLayout>` en `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. Open in **Solution Explorer**het bron bestand code-behind `MainPage.xaml.cs`. (Deze wordt gegroepeerd onder `MainPage.xaml`.)

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Zoek in de handler van `OnRecognitionButtonClicked` het bron bestand de teken `YourSubscriptionKey`reeks en vervang deze door de sleutel van uw abonnement.

1. Zoek in de handler `OnRecognitionButtonClicked` de teken reeks `YourServiceRegion` en vervang deze door de [regio](regions.md) die aan uw abonnement is gekoppeld. (Gebruik `westus` bijvoorbeeld voor het gratis proef abonnement.)

1. Vervolgens moet u een Xamarin- [service](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)maken, die wordt gebruikt om microfoon machtigingen op te vragen uit verschillende platform projecten (UWP, Android en IOS). Hiervoor voegt u de nieuwe map `Services` toe onder het project `helloworld` en maakt C# u een nieuw bron bestand onder het (Klik met de rechter muisknop op `Services` map en voegt u  > **nieuw item** **toe** > -**code bestand**) en wijzigt u de naam van de code in `IMicrophoneService.cs`. volgende fragment in dat bestand: 0 @ no__t-11

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Voorbeeld code toevoegen voor het project `helloworld.Android`

Voeg nu de C# code toe die het specifieke Android-gedeelte van de toepassing definieert.

1. Open in **Solution Explorer**onder het `helloworld.Android`-project `MainActivity.cs`.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Voeg vervolgens een specifieke Android-implementatie voor `MicrophoneService` toe door nieuwe map `Services` te maken onder het project `helloworld.Android`. Nadat u een nieuw C# bron bestand hebt gemaakt, wijzigt u de naam ervan in `MicrophoneService.cs` en kopieert u het volgende code fragment in dit bestand.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Nadat u `AndroidManifest.xml` hebt geopend onder `Properties`-map en de volgende gebruiks machtigingen voor de microfoon hebt toegevoegd tussen `<manifest>` en `</manifest>`.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Voorbeeld code toevoegen voor het project `helloworld.iOS`

Voeg nu de C# code toe die het specifieke IOS-gedeelte van de toepassing definieert en maak ook Apple Device-specifieke configuraties naar het HelloWorld. IOS-project.

1. Open in **Solution Explorer**onder het `helloworld.iOS`-project `AppDelegate.cs`.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Voeg vervolgens iOS-specifieke implementatie voor `MicrophoneService` toe door nieuwe map `Services` te maken onder het project `helloworld.iOS`. Nadat u een nieuw C# bron bestand hebt gemaakt, wijzigt u de naam ervan in `MicrophoneService.cs` en kopieert u het volgende code fragment in dit bestand.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Open info. plist onder het project `helloworld.iOS` in de tekst editor en voeg het volgende sleutel waardepaar toe onder het gedeelte dict <key>NSMicrophoneUsageDescription</key>
   <string>deze voor beeld-app vereist toegang tot de microfoon</string>
   > Opmerking: Zorg ervoor dat de `Bundle Identifier` overeenkomt met de App-ID van het inrichtings Profiel van het apparaat, anders mislukt het maken van een voor beeld dat u bent gericht op het bouwen van een iPhone-apparaat. Met iPhoneSimulator kunt u het net zo laten.

1. Als u op een Windows-PC bouwt, moet u verbinding maken met een Mac-apparaat voor het bouwen via **Hulpprogram ma's** > **IOS** > -**paar met Mac**. Volg de instructie wizard van Visual Studio om verbinding met het Mac-apparaat mogelijk te maken.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voorbeeld code toevoegen voor het project `helloworld.UWP`

Voeg nu de C# code toe die het UWP-gedeelte van de toepassing definieert.

1. Open in **Solution Explorer**onder het `helloworld.UWP`-project `MainPage.xaml.cs`.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Voeg vervolgens UWP-specifieke implementatie voor `MicrophoneService` toe door nieuwe map `Services` te maken onder het project `helloworld.UWP`. Nadat u een nieuw C# bron bestand hebt gemaakt, wijzigt u de naam ervan in `MicrophoneService.cs` en kopieert u het volgende code fragment in dit bestand.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Klik vervolgens op `Package.appxmanifest` bestand onder het `helloworld.UWP`-project in Visual Studio en onder **mogelijkheden** > -**microfoon** wordt gecontroleerd en sla het bestand op.
   > Opmerking: Als er een waarschuwing wordt weer gegeven: Certificaat bestand bestaat niet: HelloWorld. UWP_TemporaryKey. pfx, Controleer [spraak op tekst](quickstart-csharp-uwp.md) voor beeld voor meer informatie.

1. Kies in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-uwp-application"></a>De UWP-toepassing bouwen en uitvoeren

1. Stel `helloworld.UWP` in als opstart project en klik met de rechter muisknop op `helloworld.UWP`-project en kies **Build** om de toepassing te bouwen. 

1. Kies **fouten opsporen** > **fout opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP- C# toepassing voor spraak herkenning in Quick Start](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecteer **microfoon inschakelen**en selecteer wanneer de toegangs machtigings aanvraag wordt weer gegeven, **Ja**.

   ![Verzoek om toegang tot de microfoon](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecteer **spraak herkenning starten**en spreek een Engelse zin of zin uit in de microfoon van uw apparaat. Uw spraak wordt verzonden naar de spraak Services en getranscribeerd naar tekst, die wordt weer gegeven in het venster.

   ![Gebruikers interface voor spraak herkenning](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>De Android-en iOS-toepassingen bouwen en uitvoeren

Het bouwen en uitvoeren van Android-en iOS-toepassingen op het apparaat of Simulator gebeurt op vergelijk bare wijze als met UWP. Belang rijk is om ervoor te zorgen dat alle Sdk's correct zijn geïnstalleerd in de sectie `Prerequisites` van dit document.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
