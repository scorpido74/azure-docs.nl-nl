---
title: 'Quick Start: Speech herkennen C# , (Xamarin)-Speech Service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u een cross-platform C# Xamarin-toepassing voor de universeel Windows-platform (UWP), Android en IOS met behulp van de COGNITIVE Services Speech SDK. U transcribeert spraak naar tekst in realtime van de microfoon van uw apparaat of Simulator. De toepassing is gebouwd met het Speech SDK NuGet-pakket en micro soft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675601"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Quick Start: spraak herkennen met een platformoverschrijdende Xamarin-app die gebruikmaakt van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak op tekst](quickstart-csharp-uwp.md), [tekst naar spraak](quickstart-text-to-speech-csharp-uwp.md)en [spraak omzetting](quickstart-translate-speech-uwp.md).

In dit artikel ontwikkelt u een platformoverschrijdende C# toepassing met behulp van Xamarin voor de universeel Windows-platform (UWP), Android en IOS die gebruikmaken van de SDK van Azure Cognitive Services [Speech](speech-sdk.md). Het programma transcribeert spraak naar tekst in realtime van de microfoon van uw apparaat. De toepassing is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en alle edities van micro soft Visual Studio 2019.

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](get-started.md).
* Een Windows-PC met Windows 10 najaar makers update (10,0; Build 16299) of hoger en met een werkende microfoon.
* Een [Xamarin-installatie naar Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* Een [Xamarin Android-installatie in Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* Een [Xamarin IOS-installatie in Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Android-doel: 
   * Een Android-apparaat (ARM32/64, x86; API 23: Android 6,0 Marshmallow of hoger) [ingeschakeld voor ontwikkeling](https://developer.android.com/studio/debug/dev-options) met een werkende microfoon.
* Naar het doel iOS: 
    * Een iOS-apparaat (ARM64) of een iOS-Simulator (x64) is [ingeschakeld voor ontwikkeling](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) met een werkende microfoon.
* Installeer de [optionele build tools en de Windows 10 SDK voor arm/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)voor Windows ARM64 build-ondersteuning.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Voorbeeld code toevoegen voor het algemene HelloWorld-project

Het algemene HelloWorld-project bevat platform onafhankelijke implementaties voor uw platform voor meerdere platforms. Voeg nu de XAML-code toe die de gebruikers interface van de toepassing definieert en voeg C# de code toe die zich achter de implementatie bevindt.

1. Open in **Solution Explorer**onder het algemene HelloWorld-project `MainPage.xaml`.

1. In de XAML-weer gave van de ontwerp functie plaatst u het volgende XAML-fragment in de **grid** -tag tussen `<StackLayout>` en `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. Open in **Solution Explorer**het bron bestand code-behind `MainPage.xaml.cs`. Deze wordt gegroepeerd onder `MainPage.xaml`.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Zoek in de handler `OnRecognitionButtonClicked` van het bron bestand de teken reeks `YourSubscriptionKey` en vervang deze door de sleutel van uw abonnement.

1. Zoek in de handler `OnRecognitionButtonClicked` de teken reeks `YourServiceRegion` en vervang deze door de [regio](regions.md) die aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proef abonnement.

1. Vervolgens moet u een [Xamarin-service](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)maken, die wordt gebruikt om microfoon machtigingen op te vragen uit verschillende platform projecten, zoals UWP, Android en IOS. Hiervoor moet u een nieuwe map met de naam *Services* toevoegen onder het project HelloWorld en een nieuw C# bron bestand maken. U kunt met de rechter muisknop op de map *Services* klikken en  > **nieuw item** **toevoegen**  > **code bestand**. Wijzig de naam van het bestand `IMicrophoneService.cs` en plaats alle code uit het volgende fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Voeg voorbeeld code voor het HelloWorld toe. Android-project

Voeg nu de C# code toe die het Android-specifieke deel van de toepassing definieert.

1. In **Solution Explorer**, onder het HelloWorld. Android-project, open `MainActivity.cs`.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Voeg vervolgens de Android-specifieke implementatie voor `MicrophoneService` toe door de nieuwe map *Services* te maken onder het HelloWorld. Android-project. Daarna moet u een nieuw C# bron bestand maken. Wijzig de naam van het bestand `MicrophoneService.cs`. Kopieer en plak het volgende code fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Daarna opent u `AndroidManifest.xml` in de map *Eigenschappen* . Voeg de volgende gebruiks machtigings instelling voor de microfoon toe tussen `<manifest>` en `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Voorbeeld code toevoegen voor het project HelloWorld. iOS

Voeg nu de C# code toe die het IOS-specifieke deel van de toepassing definieert. Maak ook Apple Device-specifieke configuraties naar het HelloWorld. iOS-project.

1. Open `AppDelegate.cs` in **Solution Explorer**onder het project HelloWorld. IOS.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Voeg vervolgens iOS-specifieke implementatie voor `MicrophoneService` toe door de nieuwe map- *Services* te maken onder het HelloWorld.iO-project. Daarna moet u een nieuw C# bron bestand maken. Wijzig de naam van het bestand `MicrophoneService.cs`. Kopieer en plak het volgende code fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Open `Info.plist` onder het project HelloWorld. iOS in de tekst editor. Voeg het volgende sleutel paar toe onder het gedeelte dict:

   <key>NSMicrophoneUsageDescription</key> 
    voor<string>deze voor beeld-app is toegang tot de microfoon vereist</string>

   > [!NOTE] 
   > Als u op een iPhone-apparaat bouwt, moet u ervoor zorgen dat `Bundle Identifier` overeenkomt met de App-ID van het inrichtings Profiel van uw apparaat. Als dat niet het geval is, mislukt de build. Met iPhoneSimulator kunt u het net zo laten.

1. Als u op een Windows-PC bouwt, moet u verbinding maken met het Mac-apparaat voor het bouwen via **Hulpprogram ma's**  > **IOS** - > **koppelen aan Mac**. Volg de instructie wizard van Visual Studio om de verbinding met het Mac-apparaat in te scha kelen.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voeg voorbeeld code voor het HelloWorld toe. UWP-project

Voeg nu de C# code toe die het UWP gedeelte van de toepassing definieert.

1. In **Solution Explorer**, onder het HelloWorld. Open `MainPage.xaml.cs` UWP-project.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Voeg vervolgens een UWP-specifieke implementatie voor `MicrophoneService` toe door de nieuwe map *Services* te maken onder het HelloWorld. UWP-project. Daarna moet u een nieuw C# bron bestand maken. Wijzig de naam van het bestand `MicrophoneService.cs`. Kopieer en plak het volgende code fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dubbel klik vervolgens op het `Package.appxmanifest` bestand onder het HelloWorld. UWP-project in Visual Studio. Zorg ervoor dat de **microfoon** is geselecteerd onder **mogelijkheden**en sla het bestand op.

   > [!NOTE] 
   > Als er een waarschuwing wordt weer gegeven dat het certificaat bestand niet bestaat: HelloWorld. UWP_TemporaryKey. pfx, ' Controleer het voor beeld van [spraak naar tekst](quickstart-csharp-uwp.md) voor meer informatie.

1. Selecteer in de menu balk **bestand**  > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-uwp-application"></a>De UWP-toepassing bouwen en uitvoeren

1. Stel HelloWorld in. UWP als een opstart project. Klik met de rechter muisknop op het HelloWorld. UWP-project en selecteer **Build** om de toepassing te bouwen. 

1. Selecteer **fout opsporing**  > **fout opsporing starten** (of selecteer **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP- C# toepassing voor spraak herkenning in Quick Start](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecteer **microfoon inschakelen**. Wanneer het verzoek om toegang wordt weer gegeven, selecteert u **Ja**.

   ![Verzoek om toegang tot de microfoon](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecteer **spraak herkenning starten**en spreek een Engelse zin of zin uit in de microfoon van uw apparaat. Uw spraak wordt verzonden naar spraak Services en getranscribeerd naar tekst, die wordt weer gegeven in het venster.

   ![Gebruikers interface voor spraak herkenning](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>De Android-en iOS-toepassingen bouwen en uitvoeren

Het bouwen en uitvoeren van Android-en iOS-toepassingen in het apparaat of Simulator vindt op soort gelijke wijze plaats op de UWP. Zorg ervoor dat alle Sdk's correct zijn geïnstalleerd zoals vereist in de sectie ' vereisten ' van dit artikel.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
