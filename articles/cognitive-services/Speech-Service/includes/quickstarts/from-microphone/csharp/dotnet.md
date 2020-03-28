---
title: 'Snelstart: spraak herkennen van een microfoon, C# (.NET) - Spraakservice'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: c969b5e5daa4c4cfd84695fef70f0a2a5c50ce02
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924889"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is ervoor te zorgen dat u uw project open hebt in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project `Program.cs`en open .

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project. Houd er rekening mee dat u `RecognizeSpeechAsync()`een async-methode hebt gemaakt met de naam .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=5-15,43-52)]

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Voordat u een `SpeechRecognizer` object initialiseren, moet u een configuratie maken die uw abonnementssleutel en abonnementsregio gebruikt (kies de **regio-id** uit [regio.](https://aka.ms/speech/sdkregion) Voeg deze code `RecognizeSpeechAsync()` in de methode in.

> [!NOTE]
> Dit voorbeeld `FromSubscription()` gebruikt de `SpeechConfig`methode om de . Zie [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)voor een volledige lijst met beschikbare methoden.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=16)]
> De Spraak-SDK wordt standaard herkend door het gebruik van en-ons voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-a-speechrecognizer"></a>Een SpeechRecognizeer initialiseren

Laten we nu een. `SpeechRecognizer` Dit object wordt gemaakt in een gebruiksinstructie om ervoor te zorgen dat onbeheerde resources correct worden vrijgegeven. Voeg deze code `RecognizeSpeechAsync()` in de methode in, direct onder uw spraakconfiguratie.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=17-19,42)]

## <a name="recognize-a-phrase"></a>Een zin herkennen

Van `SpeechRecognizer` het object, ga je `RecognizeOnceAsync()` de methode aanroepen. Met deze methode kan de spraakservice weten dat u één woordgroep verzendt voor herkenning en dat wanneer de woordgroep is geïdentificeerd om te stoppen met het herkennen van spraak.

Voeg deze code toe in de instructie met gebruik.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=20)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten weergeven (of fouten)

Wanneer het herkenningsresultaat wordt geretourneerd door de spraakservice, wilt u er iets mee doen. We houden het simpel en printen het resultaat af op de console.

Voeg deze code `RecognizeOnceAsync()`toe in de gebruiksinstructie hieronder.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs?range=22-41)]

## <a name="check-your-code"></a>Controleer uw code

Op dit punt moet je code er zo uitzien.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-microphone/helloworld/Program.cs)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en onze spraakherkenning te testen met behulp van de Spraakservice.

1. **Compileer de code** - Kies op de menubalk van Visual Studio **Build** > **Solution**.
2. **Start uw app** - Kies op de menubalk Debug**genfout opsporing van** **foutopsporing debuggen** > of druk op **F5**.
3. **Start herkenning** - Het zal u vragen om een zin in het Engels te spreken. Uw toespraak wordt verzonden naar de spraakservice, getranscribeerd als tekst en weergegeven in de console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
