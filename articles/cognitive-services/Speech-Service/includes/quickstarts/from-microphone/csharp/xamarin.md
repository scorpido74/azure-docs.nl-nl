---
title: 'Quick Start: spraak herkennen vanuit een microfoon, C# (Xamarin)-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u een cross-platform C# Xamarin-toepassing voor de Universeel Windows-platform (UWP), Android en iOS met behulp van de Cognitive Services Speech SDK. U transcribeert spraak naar tekst in realtime van de microfoon van uw apparaat of Simulator. De toepassing is gebouwd met het Speech SDK NuGet-pakket en micro soft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: c9bcd301b19252cedd9ac9a1867ccf132a537587
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81275505"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

Als u dit al hebt gedaan, is dit geweldig. Laten we verder gaan.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Voorbeeld code toevoegen voor het algemene HelloWorld-project

Het algemene HelloWorld-project bevat platform onafhankelijke implementaties voor uw platform voor meerdere platforms. Voeg nu de XAML-code toe die de gebruikers interface van de toepassing definieert en voeg de C#-code toe achter de implementatie.

1. Open **Solution Explorer** `MainPage.xaml`in Solution Explorer onder het algemene HelloWorld-project.

1. In de XAML-weer gave van de ontwerp functie plaatst u het volgende XAML-fragment `<StackLayout>` in `</StackLayout>`de **grid** -tag tussen en:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. Open in **Solution Explorer**het bron bestand `MainPage.xaml.cs`voor de code achter. Deze wordt gegroepeerd onder `MainPage.xaml`.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Zoek in de handler van `OnRecognitionButtonClicked` het bron bestand de teken `YourSubscriptionKey`reeks en vervang deze door de sleutel van uw abonnement.


1. Zoek de `OnRecognitionButtonClicked` teken reeks `YourServiceregion`in de handler en vervang deze door de **regio-id** uit de [regio](https://aka.ms/speech/sdkregion) die aan uw abonnement is gekoppeld. (Gebruik `westus` bijvoorbeeld voor het gratis proef abonnement.)

1. Vervolgens moet u een [Xamarin-service](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)maken, die wordt gebruikt om microfoon machtigingen op te vragen uit verschillende platform projecten, zoals UWP, Android en IOS. Hiertoe voegt u een nieuwe map met de naam *Services* toe onder het project HelloWorld en maakt u een nieuw C#-bron bestand. U kunt met de rechter muisknop op de map *Services* klikken en**Nieuw item** > **code bestand** **toevoegen** > selecteren. Wijzig de naam `IMicrophoneService.cs`van het bestand en plaats alle code uit het volgende fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Voorbeeld code voor het `helloworld.Android` project toevoegen

Voeg nu de C#-code toe die het Android-specifieke deel van de toepassing definieert.

1. In **Solution Explorer**, onder het HelloWorld. Android-project, `MainActivity.cs`open.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Voeg vervolgens de Android-specifieke implementatie toe `MicrophoneService` voor door de nieuwe map *Services* te maken onder het HelloWorld. Android-project. Daarna maakt u een nieuw C#-bron bestand. Wijzig de naam `MicrophoneService.cs`van het bestand. Kopieer en plak het volgende code fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Daarna opent `AndroidManifest.xml` u onder de map *Properties (eigenschappen* ). Voeg de volgende gebruiks machtigings instelling voor de microfoon toe `<manifest>` tussen `</manifest>`en:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Voorbeeld code voor het `helloworld.iOS` project toevoegen

Voeg nu de C#-code toe die het iOS-specifieke deel van de toepassing definieert. Maak ook Apple Device-specifieke configuraties naar het HelloWorld. iOS-project.

1. Open **Solution Explorer** `AppDelegate.cs`in Solution Explorer onder het project HelloWorld. IOS.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Voeg vervolgens iOS-specifieke implementatie toe voor `MicrophoneService` door de nieuwe map- *Services* te maken onder het HelloWorld.iO-project. Daarna maakt u een nieuw C#-bron bestand. Wijzig de naam `MicrophoneService.cs`van het bestand. Kopieer en plak het volgende code fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Open `Info.plist` in het project HelloWorld. IOS in de tekst editor. Voeg het volgende sleutel paar toe onder het gedeelte dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>deze voor beeld-app vereist microfoon toegang</string>

   > [!NOTE]
   > Als u een iPhone-apparaat bouwt, moet u ervoor `Bundle Identifier` zorgen dat overeenkomt met de app-id van het inrichtings Profiel van uw apparaat. Als dat niet het geval is, mislukt de build. Met iPhoneSimulator kunt u het net zo laten.

1. Als u op een Windows-pc bouwt, moet u verbinding maken met het Mac-apparaat voor het bouwen van via **hulpprogram ma's** > **IOS** > **koppelen aan Mac**. Volg de instructie wizard van Visual Studio om de verbinding met het Mac-apparaat in te scha kelen.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voorbeeld code voor het `helloworld.UWP` project toevoegen

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voeg voorbeeld code voor het HelloWorld toe. UWP-project

Voeg nu de C#-code toe die het UWP gedeelte van de toepassing definieert.

1. In **Solution Explorer**, onder het HelloWorld. Open `MainPage.xaml.cs`UWP-project.

1. Vervang alle code hierin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Voeg vervolgens een UWP-specifieke implementatie toe voor `MicrophoneService` door de nieuwe map *Services* te maken onder het HelloWorld. UWP-project. Daarna maakt u een nieuw C#-bron bestand. Wijzig de naam `MicrophoneService.cs`van het bestand. Kopieer en plak het volgende code fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dubbel klik vervolgens op het `Package.appxmanifest` bestand onder het HelloWorld. UWP-project in Visual Studio. Zorg ervoor dat de **microfoon** is geselecteerd onder **mogelijkheden**en sla het bestand op.

1. Vervolgens dubbelklikt `Package.appxmanifest` u op bestand `helloworld.UWP` onder het project in Visual Studio en onder **capabilities** > **microfoon** wordt gecontroleerd en slaat u het bestand op.
   > Opmerking: als er een waarschuwing wordt weer gegeven: certificaat bestand bestaat niet: HelloWorld. UWP_TemporaryKey. pfx, Controleer [spraak op tekst](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) voor beeld voor meer informatie.

1. Selecteer in de menu balk de optie **bestand** > **Alles opslaan** om uw wijzigingen op te slaan.

## <a name="build-and-run-the-uwp-application"></a>De UWP-toepassing bouwen en uitvoeren

1. Stel HelloWorld in. UWP als een opstart project. Klik met de rechter muisknop op het HelloWorld. UWP-project en selecteer **Build** om de toepassing te bouwen.

1. Selecteer **fout** > **opsporing starten** (of selecteer **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP-toepassing voor spraak herkenning in C#-Quick Start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecteer **microfoon inschakelen**. Wanneer het verzoek om toegang wordt weer gegeven, selecteert u **Ja**.

   ![Verzoek om toegang tot de microfoon](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecteer **spraak herkenning starten**en spreek een Engelse zin of zin uit in de microfoon van uw apparaat. Uw gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ![Gebruikers interface voor spraak herkenning](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>De Android-en iOS-toepassingen bouwen en uitvoeren

Het bouwen en uitvoeren van Android-en iOS-toepassingen in het apparaat of Simulator vindt op soort gelijke wijze plaats op de UWP. Zorg ervoor dat alle Sdk's correct zijn geïnstalleerd zoals vereist in de sectie ' vereisten ' van dit artikel.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
