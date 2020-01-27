---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen, C++ -spraak service'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/02/2020
ms.topic: include
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b141fcdb2aacc8654999d023c4174c7eb9bc177f
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761429"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* Als dit uw eerste C++ project is, gebruikt u deze hand leiding om <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=windows" target="_blank">een leeg voorbeeld project te maken</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows" target="_blank">Installeer de Speech SDK voor uw ontwikkel omgeving</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor de intentie herkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

Open vervolgens uw project in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project en open `helloworld.cpp`.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt. Houd er rekening mee dat u een async-methode met de naam `recognizeIntent()`hebt gemaakt.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die de sleutel en locatie voor uw LUIS-Voorspellings bron gebruikt.

> [!IMPORTANT]
> Uw start sleutel-en ontwerp sleutels werken niet. U moet uw Voorspellings sleutel en-locatie gebruiken die u eerder hebt gemaakt. Zie voor meer informatie [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

Voeg deze code in de methode `recognizeIntent()` toe. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door de LUIS-Voorspellings sleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door de locatie van uw LUIS.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

In dit voor beeld wordt de `FromSubscription()` methode gebruikt om de `SpeechConfig`te bouwen. Zie [SpeechConfig-klasse](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)voor een volledige lijst met beschik bare methoden.

De spraak-SDK wordt standaard herkend door en-US voor de taal. Zie de [bron taal voor spraak opgeven](../../../../how-to-specify-source-language.md) voor de tekst voor informatie over het kiezen van de bron taal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Nu gaan we een `IntentRecognizer`maken. Voeg deze code in de `recognizeIntent()` methode toe, rechts onder uw spraak configuratie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet een `LanguageUnderstandingModel` koppelen aan de intentie herkenning en de intenties toevoegen die u wilt herkennen. We gaan de intenties gebruiken van het vooraf ontwikkelde domein voor Start Automation.

Voeg deze code toe onder uw `IntentRecognizer`. Zorg ervoor dat u `"YourLanguageUnderstandingAppId"` vervangt door de ID van uw LUIS-app.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de `RecognizeOnceAsync()`-methode aan. Met deze methode kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak. Voor de eenvoud zullen we wachten op het volt ooien van de toekomst.

Voeg deze code toe onder uw model:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig opslaan en het resultaat afdrukken naar de console.

Voeg deze code toe onder `auto result = recognizer->RecognizeOnceAsync().get();`:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien:  

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

1. **De code compileren** : Kies in de menu balk van Visual Studio **Build** > **Build-oplossing**.
2. **Start uw app** -vanuit de menu balk, kies **fout opsporing** > **fout opsporing starten** of druk op **F5**.
3. **Herkenning starten** : u wordt gevraagd om een woord groep in het Engels te spreken. Uw spraak wordt verzonden naar de spraak service, getranscribeerd als tekst en weer gegeven in de-console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
