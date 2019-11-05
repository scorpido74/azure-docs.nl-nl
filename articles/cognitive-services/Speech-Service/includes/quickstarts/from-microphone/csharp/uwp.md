---
title: 'Quick Start: spraak herkennen vanuit een microfoon C# , (UWP)-spraak service'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 0d1da9a9ef32aed1975595bb15909b9531ab2400
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503554"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=uwp)

Als u dit al hebt gedaan, is dit geweldig. Laten we verder gaan.

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is om ervoor te zorgen dat uw project in Visual Studio is geopend.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.

1. Open `MainPage.xaml`in **Solution Explorer**.

2. In de XAML-weer gave van de ontwerp functie plaatst u het volgende XAML-fragment in de **grid** -tag (tussen `<Grid>` en `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. Open in **Solution Explorer**het bron bestand van de code-behind `MainPage.xaml.cs`. (Deze wordt gegroepeerd onder `MainPage.xaml`.)

4. Vervang de code door de volgende basis code:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `SpeechRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de abonnements sleutel en de regio van het abonnement. Voeg deze code in de methode `RecognizeSpeechAsync()` toe.

> [!NOTE]
> In dit voor beeld wordt de `FromSubscription()` methode gebruikt om de `SpeechConfig`te bouwen. Zie [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/) [!code-csharpvoor een volledige lijst met beschik bare methoden [](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Een SpeechRecognizer initialiseren

Nu gaan we een `SpeechRecognizer`maken. Dit object wordt gemaakt in een using-instructie om ervoor te zorgen dat onbeheerde bronnen goed worden vrijgegeven. Voeg deze code in de `RecognizeSpeechAsync()` methode toe, rechts onder uw spraak configuratie.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Een woord groep herkennen

Vanuit het `SpeechRecognizer`-object roept u de `RecognizeOnceAsync()`-methode aan. Met deze methode kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak.

Voeg in de instructie using deze code toe: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig en het resultaat afdrukken in het status paneel.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

U bent nu klaar om uw toepassing te bouwen en te testen.

1. Kies in de menu balk de optie **build** > **Build** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout opsporing** > **fout opsporing starten** (of druk op **F5**) om de toepassing te starten. Het venster **HelloWorld** wordt weer gegeven.

   ![Voor beeld van een UWP- C# toepassing voor spraak herkenning in Quick Start](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecteer **microfoon inschakelen**en selecteer wanneer de toegangs machtigings aanvraag wordt weer gegeven, **Ja**.

   ![Verzoek om toegang tot de microfoon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **Speech recognition with microphone input** en zeg een Engelse woordgroep of zin in de microfoon van uw apparaat. Uw spraak wordt verzonden naar de spraak Services en getranscribeerd naar tekst, die wordt weer gegeven in het venster.

   ![Gebruikers interface voor spraak herkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
