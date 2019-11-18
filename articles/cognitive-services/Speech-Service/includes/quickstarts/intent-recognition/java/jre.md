---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen, Java-spraak service'
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
ms.openlocfilehash: 2aa8311c9284d9aff3c50fcec30bc8ab9df6b11b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125457"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
>
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Een LUIS-toepassing maken en een eindpunt sleutel ophalen](../../../../quickstarts/create-luis.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=jre)

## <a name="open-your-project"></a>Uw project openen

Laad uw project en open `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,69-76)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van uw LUIS-eindpunt sleutel en-regio. Voeg deze code in het blok try/catch toe aan het hoofd

In dit voor beeld wordt de `FromSubscription()` methode gebruikt om de `SpeechConfig`te bouwen. Zie [SpeechConfig-klasse](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)voor een volledige lijst met beschik bare methoden.

> [!NOTE]
> Het is belang rijk dat u de LUIS-eindpunt sleutel gebruikt en niet de Starter-of ontwerp sleutels, omdat alleen de eindpunt sleutel geldig is voor spraak op de intentie herkenning. Zie [een Luis-toepassing maken en een eindpunt sleutel ophalen](~/articles/cognitive-services/Speech-Service/quickstarts/create-luis.md) voor instructies over het ophalen van de juiste sleutel.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Nu gaan we een `IntentRecognizer`maken. Voeg deze code toe aan de rechter kant onder uw spraak configuratie.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet nu een `LanguageUnderstandingModel` koppelen aan de intentie herkenning en de intenties toevoegen die u wilt herkennen.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-36)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de `recognizeOnceAsync()`-methode aan. Met deze methode kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=41)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig opslaan en het resultaat afdrukken naar de console.

Voeg de volgende code toe onder de aanroep van `recognizeOnceAsync()`: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=44-65)]

## <a name="release-resources"></a>Release resources

Het is belang rijk om de spraak bronnen vrij te geven wanneer u klaar bent met het gebruik ervan. Voeg deze code toe aan het einde van het blok try/catch: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=67-68)]

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien:  
(Er zijn enkele opmerkingen aan deze versie toegevoegd) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-76)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Druk op F11 of selecteer **Uitvoeren** > **Fouten opsporen**.
De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]