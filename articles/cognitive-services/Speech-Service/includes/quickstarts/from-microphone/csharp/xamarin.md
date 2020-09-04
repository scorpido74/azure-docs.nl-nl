---
title: 'Quickstart: Spraak herkennen vanaf een microfoon, C# (Xamarin) - Speech-service'
titleSuffix: Azure Cognitive Services
description: In dit artikel maakt u een platformoverschrijdende C# Xamarin-toepassing voor Universal Windows Platform (UWP), Android en iOS met behulp van de Speech-SDK van Cognitive Services. U gaat realtime spraak naar tekst transcriberen via de microfoon van uw apparaat of simulator. De toepassing wordt gemaakt met het NuGet-pakket van de Speech SDK en Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48a80d7367943c2c86fd3822a5c236b0cdf11846
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926407"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../get-started.md)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

Als u dat al hebt gedaan: geweldig. Laten we doorgaan.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Voorbeeldcode toevoegen voor het algemene project helloworld

Het algemene project helloworld bevat platformonafhankelijke implementaties voor uw platformoverschrijdende toepassing. Voeg nu de XAML-code toe waarmee de gebruikersinterface van de toepassing wordt gedefinieerd en voeg de C#-code achter de implementatie toe.

1. Open in **Solution Explorer**, onder het algemene project helloworld, `MainPage.xaml`.

1. Voeg in de XAML-weergave van de ontwerpfunctie het volgende XAML-fragment toe aan de tag **Grid**, tussen `<StackLayout>` en `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. Open in **Solution Explorer** het code-behind-bronbestand `MainPage.xaml.cs`. Het is gegroepeerd onder `MainPage.xaml`.

1. Vervang alle code in het bestand door het volgende codefragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Zoek in de handler `OnRecognitionButtonClicked` van het bronbestand de tekenreeks `YourSubscriptionKey` en vervang deze door uw abonnementssleutel.


1. Zoek in de handler `OnRecognitionButtonClicked` de tekenreeks `YourServiceregion` en vervang deze door de **Regio-id** van de [regio](https://aka.ms/speech/sdkregion) die aan uw abonnement is gekoppeld. 

1. Vervolgens moet u een [Xamarin-service](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/) maken, die wordt gebruikt om microfoonmachtigingen op te vragen uit verschillende platformprojecten, zoals UWP, Android en iOS. Voeg hiertoe een nieuwe map met de naam *Services* toe onder het project helloworld en maak daaronder een nieuw C#-bronbestand. U kunt met de rechtermuisknop op de map *Services* klikken en **Toevoegen** > **Nieuw item** > **Codebestand** selecteren. Wijzig de naam van het bestand in `IMicrophoneService.cs` en plaats alle code uit het volgende fragment in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Voorbeeldcode toevoegen voor het project `helloworld.Android`

Voeg nu de C#-code toe waarmee het Android-specifieke deel van de toepassing wordt gedefinieerd.

1. Open in **Solution Explorer**, onder het project helloworld.Android, `MainActivity.cs`.

1. Vervang alle code in het bestand door het volgende codefragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Voeg vervolgens een Android-specifieke implementatie voor `MicrophoneService` toe door de nieuwe map *Services* te maken onder het project helloworld.Android. Maak daaronder vervolgens een nieuw C#-bronbestand. Wijzig de naam van het bestand in `MicrophoneService.cs`. Kopieer het volgende codefragment en plak het in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Open vervolgens `AndroidManifest.xml` onder de map *Eigenschappen*. Voeg de volgende 'uses-permission'-instelling voor de microfoon toe tussen `<manifest>` en `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Voorbeeldcode toevoegen voor het project `helloworld.iOS`

Voeg nu de C#-code toe waarmee het iOS-specifieke deel van de toepassing wordt gedefinieerd. Maak ook configuraties specifiek voor Apple-apparaten voor het project helloworld.iOS.

1. Open in **Solution Explorer**, onder het project helloworld.iOS, `AppDelegate.cs`.

1. Vervang alle code in het bestand door het volgende codefragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Voeg vervolgens een iOS-specifieke implementatie voor `MicrophoneService` toe door de nieuwe map *Services* te maken onder het project helloworld.iOS. Maak daaronder vervolgens een nieuw C#-bronbestand. Wijzig de naam van het bestand in `MicrophoneService.cs`. Kopieer het volgende codefragment en plak het in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Open `Info.plist` onder het project helloworld.iOS in de teksteditor. Voeg het volgende sleutel-waardepaar toe onder de sectie dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Voor deze voorbeeld-app is toegang tot de microfoon vereist</string>

   > [!NOTE]
   > Als u compileert voor een iPhone-apparaat, moet u ervoor zorgen dat `Bundle Identifier` overeenkomt met de app-id van het inrichtingsprofiel van uw apparaat. Anders mislukt het compileren. Voor iPhoneSimulator hoeft u de waarde niet te wijzigen.

1. Als u op een Windows-pc compileert, moet u voor het compileren verbinding maken met het Mac-apparaat via **Hulpprogramma's** > **iOS** > **Aan Mac koppelen**. Volg de instructiewizard van Visual Studio om de verbinding met het Mac-apparaat tot stand te brengen.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voorbeeldcode toevoegen voor het project `helloworld.UWP`

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Voorbeeldcode toevoegen voor het project helloworld.UWP

Voeg nu de C#-code toe waarmee het UWP-specifieke deel van de toepassing wordt gedefinieerd.

1. Open in **Solution Explorer**, onder het project helloworld.UWP, `MainPage.xaml.cs`.

1. Vervang alle code in het bestand door het volgende codefragment:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Voeg vervolgens een iOS-specifieke implementatie voor `MicrophoneService` toe door de nieuwe map *Services* te maken onder het project helloworld.UWP. Maak daaronder vervolgens een nieuw C#-bronbestand. Wijzig de naam van het bestand in `MicrophoneService.cs`. Kopieer het volgende codefragment en plak het in dat bestand:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dubbelklik vervolgens op het bestand `Package.appxmanifest` onder het project helloworld.UWP in Visual Studio. Zorg ervoor dat onder **Mogelijkheden** de optie **Microfoon** is geselecteerd en sla het bestand op.

1. Klik vervolgens op bestand `Package.appxmanifest` onder het project `helloworld.UWP` in Visual Studio en zorg ervoor dat onder **Mogelijkheden** > **Microfoon** is ingeschakeld en sla het bestand op.
   > Opmerking: Als de volgende waarschuwing wordt weergegeven: Certificaatbestand bestaat niet: helloworld.UWP_TemporaryKey.pfx, raadpleeg dan het voorbeeld [spraak-naar-tekst](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) voor meer informatie.

1. Kies in de menubalk **Bestand** > **Alles opslaan** om de wijzigingen op te slaan.

## <a name="build-and-run-the-uwp-application"></a>De UWP-toepassing compileren en uitvoeren

1. Stel helloworld.UWP in als een opstartproject. Klik met de rechtermuisknop op het project helloworld.UWP en selecteer **Build** om de toepassing te compileren.

1. Selecteer **Debug** > **Start Debugging** (of selecteer **F5**) om de toepassing te starten. Het venster **helloworld** wordt weergegeven.

   ![Voorbeeld van UWP-spraakherkenningstoepassing in C# - quickstart](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecteer **Microfoon inschakelen**. Wanneer de toegangsaanvraag wordt weergegeven, selecteert u **Ja**.

   ![Verzoek om toegang tot microfoon](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecteer **Spraakherkenning starten** en zeg een Engelse woordgroep of zin in de microfoon van uw apparaat. Uw gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ![Gebruikersinterface voor spraakherkenning](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>De Android- en iOS-toepassing compileren en uitvoeren

Het compileren en uitvoeren van een Android- en iOS-toepassing in het apparaat of de simulator is vergelijkbaar met dat van de UWP-toepassing. Zorg ervoor dat alle SDK's correct zijn geïnstalleerd volgens de sectie Vereisten van dit artikel.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
