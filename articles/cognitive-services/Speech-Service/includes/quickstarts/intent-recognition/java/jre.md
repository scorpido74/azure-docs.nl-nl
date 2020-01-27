---
title: 'Snelstartgids: spraak, intenties en entiteiten herkennen, Java-spraak service'
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
ms.openlocfilehash: c5d70bba32df5940d929482e37317d40bd496676
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761458"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* Als dit uw eerste Java-project is, gebruikt u deze hand leiding om <a href="~/articles/cognitive-services/Speech-Service/quickstarts/create-project.md?tabs=jre" target="_blank">een leeg voorbeeld project te maken</a>.
* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">Installeer de Speech SDK voor uw ontwikkel omgeving</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor de intentie herkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Uw project openen

1. Open de IDE van uw voor keur.
2. Laad uw project en open `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die de sleutel en locatie voor uw LUIS-Voorspellings bron gebruikt.  

Voeg deze code toe aan het blok try/catch in `main()`. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door de LUIS-Voorspellings sleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door de locatie van uw LUIS.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

In dit voor beeld wordt de `FromSubscription()` methode gebruikt om de `SpeechConfig`te bouwen. Zie [SpeechConfig-klasse](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)voor een volledige lijst met beschik bare methoden.

De spraak-SDK wordt standaard herkend door en-US voor de taal. Zie de [bron taal voor spraak opgeven](../../../../how-to-specify-source-language.md) voor de tekst voor informatie over het kiezen van de bron taal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Nu gaan we een `IntentRecognizer`maken. Voeg deze code toe aan de rechter kant onder uw spraak configuratie.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet een `LanguageUnderstandingModel` koppelen aan de intentie herkenning en de intenties toevoegen die u wilt herkennen. We gaan de intenties gebruiken van het vooraf ontwikkelde domein voor Start Automation.

Voeg deze code toe onder uw `IntentRecognizer`. Zorg ervoor dat u `"YourLanguageUnderstandingAppId"` vervangt door de ID van uw LUIS-app.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de `recognizeOnceAsync()`-methode aan. Met deze methode kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak.

Voeg deze code toe onder uw model:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig opslaan en het resultaat afdrukken naar de console.

Voeg deze code toe onder de aanroep van `recognizeOnceAsync()`: [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Release resources

Het is belang rijk om de spraak bronnen vrij te geven wanneer u klaar bent met het gebruik ervan. Voeg deze code toe aan het einde van het blok try/catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien:  

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Druk op F11 of selecteer **Uitvoeren** > **Fouten opsporen**.
De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
