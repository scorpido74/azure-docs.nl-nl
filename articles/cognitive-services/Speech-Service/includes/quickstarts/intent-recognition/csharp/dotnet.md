---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen, C# -spraak service'
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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a54d01aa78ad77c9328f79f31d21da570e7f5676
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503820"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Een LUIS-toepassing maken en een eindpunt sleutel ophalen](../../../../quickstarts/create-luis.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is om ervoor te zorgen dat uw project in Visual Studio is geopend.

1. Start Visual Studio 2019.
2. Laad uw project en open `Program.cs`.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt. Houd er rekening mee dat u een async-methode met de naam `RecognizeIntentAsync()`hebt gemaakt.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van uw LUIS Endpoing-sleutel en-regio. Voeg deze code in de methode `RecognizeIntentAsync()` toe.

In dit voor beeld wordt de `FromSubscription()` methode gebruikt om de `SpeechConfig`te bouwen. Zie [SpeechConfig-klasse](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)voor een volledige lijst met beschik bare methoden.

> [!NOTE]
> Het is belang rijk dat u de LUIS-eindpunt sleutel gebruikt en niet de Starter-of Authroing-sleutels, omdat alleen de eindpunt sleutel geldig is voor spraak op intentie herkenning. Zie [een Luis-toepassing maken en een eindpunt sleutel ophalen](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) voor instructies over het ophalen van de juiste sleutel.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

## <a name="initialize-a-intentrecognizer"></a>Een IntentRecognizer initialiseren

Nu gaan we een `IntentRecognizer`maken. Dit object wordt gemaakt in een using-instructie om ervoor te zorgen dat onbeheerde bronnen goed worden vrijgegeven. Voeg deze code in de `RecognizeIntentAsync()` methode toe, rechts onder uw spraak configuratie.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=28-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet nu een `LanguageUnderstandingModel` koppelen aan de intentie herkenning en de intenties toevoegen die u wilt herkennen.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=31-35)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de `RecognizeOnceAsync()`-methode aan. Met deze methode kan de speech-service weten dat u één woord groep verzendt voor herkenning en dat zodra de woord groep is geïdentificeerd om reconizinge speech te stoppen.

Voeg in de instructie using deze code toe: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig opslaan en het resultaat afdrukken naar de console.

Voeg in de instructie using onder `RecognizeOnceAsync()`de volgende code toe: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=48-75)]

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien: (er zijn enkele opmerkingen aan deze versie toegevoegd) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=5-86)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

1. **De code compileren** : Kies in de menu balk van Visual Studio **Build** > **Build-oplossing**.
2. **Start uw app** -vanuit de menu balk, kies **fout opsporing** > **fout opsporing starten** of druk op **F5**.
3. **Herkenning starten** : u wordt gevraagd om een woord groep in het Engels te spreken. Uw spraak wordt verzonden naar de spraak service, getranscribeerd als tekst en weer gegeven in de-console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
