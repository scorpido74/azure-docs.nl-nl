---
title: 'Quickstart: Spraak herkennen vanaf een microfoon, C# (UWP) - Speech-service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: a28a35bb57c759c58105c12673cb233d5b6f2e75
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376823"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=uwp&pivots=programming-language-csharp)
> * Ervoor zorgen dat u toegang tot een microfoon hebt voor het vastleggen van audio

Als u dat al hebt gedaan: geweldig. Laten we doorgaan.

## <a name="open-your-project-in-visual-studio"></a>Open uw project in Visual Studio

De eerste stap is het openen van uw project in Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen.

1. In **Solution Explorer** opent u `MainPage.xaml`.

2. Voeg in de XAML-weergave van de ontwerpfunctie het volgende XAML-fragment toe aan de tag **Grid** (tussen `<Grid>` en `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. Open in **Solution Explorer** het code-behind-bronbestand `MainPage.xaml.cs`. (Het is gegroepeerd onder `MainPage.xaml`.)

4. Vervang de code door de volgende basiscode:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `SpeechRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de abonnementssleutel en de regio van het abonnement. Voeg deze code toe in de methode `SpeechRecognitionFromMicrophone_ButtonClicked()`.

> [!NOTE]
> In dit voorbeeld wordt de methode `FromSubscription()` gebruikt om de `SpeechConfig` te maken. Zie [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)] voor een volledige lijst met beschikbare methoden

## <a name="initialize-a-speechrecognizer"></a>Een SpeechRecognizer initialiseren

Laten we nu een `SpeechRecognizer` maken. Dit object wordt in een using-instructie gemaakt om de juiste vrijgave van niet-beheerde resources te verzekeren. Voeg deze code toe in de methode `SpeechRecognitionFromMicrophone_ButtonClicked()`, recht onder uw Speech-configuratie.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Een woordgroep herkennen

Vanuit het `SpeechRecognizer`-object roept u de methode `RecognizeOnceAsync()` aan. Met deze methode laat u de Speech-service weten dat u één woordgroep verstuurt voor herkenning en dat er kan worden gestopt met het herkennen van spraak zodra de woordgroep is geïdentificeerd.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten (of fouten) weergeven

Wanneer het herkenningsresultaat wordt geretourneerd door de Speech-service, wilt u daar iets mee doen. We zullen het eenvoudig houden en het resultaat afdrukken naar het statuspaneel.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en testen.

1. Kies in de menubalk **Build** > **Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Debug** > **Start Debugging** (of druk op **F5**) om de toepassing te starten. Het venster **helloworld** wordt weergegeven.

   ![Voorbeeld van UWP-spraakherkenningstoepassing in C# - quickstart](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecteer **Enable Microphone**, en selecteer **Yes** wanneer het toegangsverzoek verschijnt.

   ![Verzoek om toegang tot microfoon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **Speech recognition with microphone input** en zeg een Engelse woordgroep of zin in de microfoon van uw apparaat. Uw gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ![Gebruikersinterface voor spraakherkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

