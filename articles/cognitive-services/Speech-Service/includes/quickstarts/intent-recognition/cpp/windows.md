---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen, C++ -spraak service'
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
ms.openlocfilehash: 277548c859370a700973d9f60556fe38e94548b0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280427"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
>
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Een Language Understanding-toepassing (LUIS) maken en een eindpunt sleutel ophalen](../../../../quickstarts/create-luis.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

De eerste stap is om ervoor te zorgen dat uw project in Visual Studio is geopend.

1. Start Visual Studio 2019.
2. Laad uw project en open `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt. Houd er rekening mee dat u een async-methode met de naam `recognizeIntent()`hebt gemaakt.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,73-81)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van uw LUIS-eindpunt sleutel en-regio. Voeg deze code in de methode `recognizeIntent()` toe.

In dit voor beeld wordt de `FromSubscription()` methode gebruikt om de `SpeechConfig`te bouwen. Zie [SpeechConfig-klasse](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)voor een volledige lijst met beschik bare methoden.

> [!NOTE]
> Het is belang rijk dat u de LUIS-eindpunt sleutel gebruikt en niet de Starter-of ontwerp sleutels, omdat alleen de eindpunt sleutel geldig is voor spraak op de intentie herkenning. Zie [een Luis-toepassing maken en een eindpunt sleutel ophalen](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) voor instructies over het ophalen van de juiste sleutel.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Nu gaan we een `IntentRecognizer`maken. Voeg deze code in de `recognizeIntent()` methode toe, rechts onder uw spraak configuratie.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet nu een `LanguageUnderstandingModel` koppelen aan de intentie herkenning en de intenties toevoegen die u wilt herkennen.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-34)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de `RecognizeOnceAsync()`-methode aan. Met deze methode kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak.
Voor de eenvoud zullen we wachten op het volt ooien van de toekomst.

Voeg in de instructie using deze code toe: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=44)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig opslaan en het resultaat afdrukken naar de console.

Voeg in de instructie using onder `RecognizeOnceAsync()`de volgende code toe: [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=47-72)]

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien:  
(Er zijn enkele opmerkingen aan deze versie toegevoegd) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-81)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

1. **De code compileren** : Kies in de menu balk van Visual Studio **Build** > **Build-oplossing**.
2. **Start uw app** -vanuit de menu balk, kies **fout opsporing** > **fout opsporing starten** of druk op **F5**.
3. **Herkenning starten** : u wordt gevraagd om een woord groep in het Engels te spreken. Uw spraak wordt verzonden naar de spraak service, getranscribeerd als tekst en weer gegeven in de-console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]