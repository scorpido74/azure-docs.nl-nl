---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d819eaa5c90e304a642efd3a6f2458cab4eefe7f
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422212"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Installeer de Speech-SDK voor uw ontwikkel omgeving en maak een leeg voorbeeld<span class="docon docon-navigate-external x-hidden-focus"></span>project</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor de intentie herkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Uw project openen

1. Open de IDE van uw voor keur.
2. Maak een nieuw project en maak een bestand `quickstart.py`met de naam en open het.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `IntentRecognizer` object kunt initialiseren, moet u een configuratie maken die de sleutel en locatie voor uw Luis-Voorspellings bron gebruikt.

Voeg deze code in `quickstart.py`in. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door uw Luis-Voorspellings sleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door uw Luis-locatie. **Regio-id** uit [regio](https://aka.ms/speech/sdkregion) gebruiken

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Met dit voor beeld wordt `SpeechConfig` het object gemaakt met behulp van Luis-sleutel en-regio. Zie [SpeechConfig-klasse](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)voor een volledige lijst met beschik bare methoden.

De spraak-SDK wordt standaard herkend door en-US voor de taal. Zie de [bron taal voor spraak opgeven](../../../../how-to-specify-source-language.md) voor de tekst voor informatie over het kiezen van de bron taal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Nu gaan we een `IntentRecognizer`maken. Voeg deze code toe aan de rechter kant onder uw spraak configuratie.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet een `LanguageUnderstandingModel` met de intentie herkenning koppelen en de intenties toevoegen die u wilt herkennen. We gaan de intenties gebruiken van het vooraf ontwikkelde domein voor Start Automation.

Voeg deze code toe onder `IntentRecognizer`uw. Zorg ervoor dat u vervangt `"YourLanguageUnderstandingAppId"` door de id van uw Luis-app. 

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer` object roept u de `recognize_once()` -methode aan. Met deze methode kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak.

Voeg deze code toe onder uw model.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig opslaan en het resultaat afdrukken naar de console.

Voeg deze code toe `recognize_once()`onder uw aanroep naar.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien.

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Voer het voor beeld uit vanaf de-console of in uw IDE:

```
python quickstart.py
```

De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
