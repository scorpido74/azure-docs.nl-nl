---
title: 'Snelstart: spraak herkennen van een microfoon, C# (UWP) - Spraakservice'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: 3e7cd96b979fb97d7b50b84907881fe59d03e295
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659613"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

Als je dit al gedaan hebt, geweldig. Laten we doorgaan.

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is ervoor te zorgen dat u uw project open hebt in Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project.

1. Open **in** `MainPage.xaml`Solution Explorer .

2. Plaats in de XAML-weergave van de ontwerper het volgende XAML-fragment in de **rastertag** (tussen `<Grid>` en): `</Grid>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. Open **in Solution Explorer**het broncodebestand `MainPage.xaml.cs`met code achter . (Het is gegroepeerd `MainPage.xaml`onder .)

4. Vervang de code door de volgende basiscode:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Voordat u een `SpeechRecognizer` object initialiseren, moet u een configuratie maken die uw abonnementssleutel en abonnementsregio gebruikt. Voeg deze code `RecognizeSpeechAsync()` in de methode in.

> [!NOTE]
> Dit voorbeeld `FromSubscription()` gebruikt de `SpeechConfig`methode om de . Zie [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/) voor een volledige lijst met beschikbare methoden[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Een SpeechRecognizeer initialiseren

Laten we nu een. `SpeechRecognizer` Dit object wordt gemaakt in een gebruiksinstructie om ervoor te zorgen dat onbeheerde resources correct worden vrijgegeven. Voeg deze code `RecognizeSpeechAsync()` in de methode in, direct onder uw spraakconfiguratie.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Een zin herkennen

Van `SpeechRecognizer` het object, ga je `RecognizeOnceAsync()` de methode aanroepen. Met deze methode kan de spraakservice weten dat u één woordgroep verzendt voor herkenning en dat wanneer de woordgroep is geïdentificeerd om te stoppen met het herkennen van spraak.

Voeg deze code toe in de instructie met gebruik.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten weergeven (of fouten)

Wanneer het herkenningsresultaat wordt geretourneerd door de spraakservice, wilt u er iets mee doen. We houden het simpel en printen het resultaat af op het statuspaneel.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

Nu bent u klaar om uw toepassing te bouwen en te testen.

1. Kies**build-oplossing** **bouwen** > om de toepassing te bouwen op de menubalk. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **debuggen** (of druk op **F5)** om de toepassing te starten. Het **helloworld** venster verschijnt.

   ![Voorbeeld van UWP-toepassing voor spraakherkenning in C# - snel starten](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Selecteer **Microfoon inschakelen**en wanneer de aanvraag voor toegangsmachtigingen verschijnt, selecteert u **Ja**.

   ![Machtigingsaanvraag voor toegang voor microfoon](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecteer **Speech recognition with microphone input** en zeg een Engelse woordgroep of zin in de microfoon van uw apparaat. Uw gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ![Gebruikersinterface voor spraakherkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](../footer.md)]
