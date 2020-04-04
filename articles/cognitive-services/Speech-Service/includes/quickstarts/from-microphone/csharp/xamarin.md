---
title: 'Snelstart: Spraak herkennen van een microfoon, C# (Xamarin) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u een cross-platform C# Xamarin-toepassing voor het Universal Windows Platform (UWP), Android en iOS met behulp van de Cognitive Services Speech SDK. U transcribeert spraak naar tekst in realtime vanaf de microfoon van uw apparaat of simulator. De applicatie is gebouwd met het Speech SDK NuGet Package en Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 98181bac7cf9aca85766d78fcd753ad8bdabc3ee
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659065"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

Als je dit al gedaan hebt, geweldig. Laten we doorgaan.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Voorbeeldcode toevoegen voor het algemene helloworld-project

Het gemeenschappelijke helloworld project bevat platformonafhankelijke implementaties voor uw cross-platform applicatie. Voeg nu de XAML-code toe die de gebruikersinterface van de toepassing definieert en voeg de C#-code achter de implementatie toe.

1. In **Solution Explorer,** onder de gemeenschappelijke helloworld project, open `MainPage.xaml`.

1. Plaats in de XAML-weergave van de ontwerper het volgende `<StackLayout>` XAML-fragment in de **rastertag** tussen en `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. Open **in Solution Explorer**het broncodebestand `MainPage.xaml.cs`met code achter . Het is gegroepeerd `MainPage.xaml`onder.

1. Vervang alle code daarin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Zoek in de `OnRecognitionButtonClicked` handler van het `YourSubscriptionKey`bronbestand de tekenreeks en vervang deze door uw abonnementssleutel.


1. Zoek `OnRecognitionButtonClicked` in de handler `YourServiceregion`de tekenreeks en vervang deze door de **regio-id** van [regio die](https://aka.ms/speech/sdkregion) is gekoppeld aan uw abonnement. (Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.)

1. Vervolgens moet u een [Xamarin-service](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/)maken, die wordt gebruikt om microfoonmachtigingen van verschillende platformprojecten, zoals UWP, Android en iOS, op te vragen. Voeg daarvoor een nieuwe map met de naam *Services* toe onder het helloworld-project en maak er een nieuw C#-bronbestand onder. U met de rechtermuisknop op **Add** > de map *Services* klikken en nieuw > **itemcodebestand**toevoegen selecteren .**New Item** Wijzig de `IMicrophoneService.cs`naam van het bestand en plaats alle code uit het volgende fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Voorbeeldcode voor `helloworld.Android` het project toevoegen

Voeg nu de C#-code toe die het Android-specifieke deel van de toepassing definieert.

1. In **Solution Explorer,** onder de helloworld. Android-project, `MainActivity.cs`open .

1. Vervang alle code daarin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Voeg vervolgens Android-specifieke `MicrophoneService` implementatie toe voor door de nieuwe map *Services* te maken onder de helloworld. Android-project. Maak daarna een nieuw C#-bronbestand eronder. Wijzig de `MicrophoneService.cs`naam van het bestand . Kopieer en plak het volgende codefragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Daarna wordt `AndroidManifest.xml` u geopend onder de map *Eigenschappen.* Voeg de volgende instelling voor gebruiksmachtigingen toe voor de microfoon tussen `<manifest>` en: `</manifest>`

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Voorbeeldcode voor `helloworld.iOS` het project toevoegen

Voeg nu de C#-code toe die het iOS-specifieke deel van de toepassing definieert. Maak ook Apple-apparaatspecifieke configuraties voor het helloworld.iOS-project.

1. In **Solution Explorer,** onder het helloworld.iOS-project, open `AppDelegate.cs`.

1. Vervang alle code daarin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Voeg vervolgens iOS-specifieke `MicrophoneService` implementatie toe voor door de nieuwe map *Services* te maken onder het helloworld.iO project. Maak daarna een nieuw C#-bronbestand eronder. Wijzig de `MicrophoneService.cs`naam van het bestand . Kopieer en plak het volgende codefragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Open `Info.plist` onder het helloworld.iOS-project in de teksteditor. Voeg het volgende sleutelwaardepaar toe onder de sectie dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Deze voorbeeld-app vereist microfoontoegang</string>

   > [!NOTE]
   > Als u bouwt voor een iPhone-apparaat, moet u ervoor zorgen dat `Bundle Identifier` deze overeenkomt met de inrichtingsprofiel-app-id van uw apparaat. Anders zal de build mislukken. Met iPhoneSimulator kun je het laten zoals het is.

1. Als u bouwt op een Windows-pc, maakt u een verbinding met het Mac-apparaat voor het bouwen via **Extra** > **iOS-paar** > **naar Mac.** Volg de instructiewizard van Visual Studio om de verbinding met het Mac-apparaat in te schakelen.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voorbeeldcode voor `helloworld.UWP` het project toevoegen

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voeg voorbeeldcode toe voor de helloworld. UWP-project

Voeg nu de C#-code toe die het UWP-specifieke deel van de toepassing definieert.

1. In **Solution Explorer,** onder de helloworld. UWP project, `MainPage.xaml.cs`open .

1. Vervang alle code daarin door het volgende fragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Voeg vervolgens een UWP-specifieke `MicrophoneService` implementatie toe voor door de nieuwe map *Services* te maken onder de helloworld. UWP-project. Maak daarna een nieuw C#-bronbestand eronder. Wijzig de `MicrophoneService.cs`naam van het bestand . Kopieer en plak het volgende codefragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dubbelklik vervolgens op `Package.appxmanifest` het bestand onder de helloworld. UWP-project binnen Visual Studio. Controleer **onder Mogelijkheden**of **microfoon** is geselecteerd en sla het bestand op.

1. Volgende `Package.appxmanifest` dubbelklikbestand `helloworld.UWP` onder het project in Visual Studio en onder **Mogelijkheden** > **Microfoon** wordt gecontroleerd en slaat het bestand op.
   > Opmerking: In het geval u waarschuwing ziet: Certificaatbestand bestaat niet: helloworld. UWP_TemporaryKey.pfx, controleer [spraak naar tekst](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) voorbeeld voor meer informatie.

1. Selecteer Alles**opslaan** **op** > de menubalk om de wijzigingen op te slaan.

## <a name="build-and-run-the-uwp-application"></a>De UWP-toepassing bouwen en uitvoeren

1. Stel helloworld neer. UWP als opstartproject. Klik met de rechtermuisknop op de helloworld. UWP-project en selecteer **Bouwen** om de toepassing te bouwen.

1. Selecteer **Foutopsporing debuggen** > **debuggen** (of selecteer **F5)** om de toepassing te starten. Het **helloworld** venster verschijnt.

   ![Voorbeeld van UWP-toepassing voor spraakherkenning in C# - snel starten](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecteer **Microfoon inschakelen**. Wanneer de aanvraag voor toegangsmachtigingen wordt weergegeven, selecteert u **Ja**.

   ![Machtigingsaanvraag voor toegang voor microfoon](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecteer **Spraakherkenning starten**en spreek een Engelse zin of zin in de microfoon van uw apparaat. Uw gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ![Gebruikersinterface voor spraakherkenning](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>De Android- en iOS-toepassingen bouwen en uitvoeren

Het bouwen en uitvoeren van Android- en iOS-toepassingen in het apparaat of de simulator gebeurt op een vergelijkbare manier als het UWP. Zorg ervoor dat alle SDK's correct zijn geïnstalleerd zoals vereist in het gedeelte 'Voorwaarden' van dit artikel.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](../footer.md)]
