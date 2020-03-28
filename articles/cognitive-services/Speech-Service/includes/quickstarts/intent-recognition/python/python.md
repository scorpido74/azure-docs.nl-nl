---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 724f52317ce2afda023ae0514a330da0032e8710
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925619"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md" target="_blank">Installeer de Speech SDK voor uw ontwikkelomgeving<span class="docon docon-navigate-external x-hidden-focus"></span>en maak een leeg voorbeeldproject.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor intentieherkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Uw project openen

1. Open uw favoriete IDE.
2. Maak een nieuw project `quickstart.py`en maak een bestand genaamd, open het vervolgens.

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Voordat u een `IntentRecognizer` object initialiseren, moet u een configuratie maken die de sleutel en locatie voor uw LUIS-voorspellingsbron gebruikt.

Deze code `quickstart.py`invoegen in . Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door uw LUIS-voorspellingssleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door uw LUIS-locatie. **Regio-id** gebruiken vanuit [regio](https://aka.ms/speech/sdkregion)

>[!TIP]
> Zie [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition)als u hulp nodig hebt bij het vinden van deze waarden.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

In dit voorbeeld `SpeechConfig` wordt het object opgebouwd met LUIS-toets en -gebied. Zie [SpeechConfig Class](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)voor een volledige lijst met beschikbare methoden.

De Spraak-SDK wordt standaard herkend door het gebruik van en-ons voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizeer initialiseren

Laten we nu een. `IntentRecognizer` Plaats deze code direct onder uw spraakconfiguratie.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en Intents toevoegen

U moet een `LanguageUnderstandingModel` medewerker van een intentieherkenning koppelen en de gewenste intenties toevoegen die u wilt herkennen. We gaan intents uit het vooraf gebouwde domein gebruiken voor domotica.

Voeg deze code `IntentRecognizer`in onder uw . Zorg ervoor dat `"YourLanguageUnderstandingAppId"` u uw LUIS-app-id vervangt. 

>[!TIP]
> Zie [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition)als u hulp nodig hebt bij het vinden van deze waarde.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Van `IntentRecognizer` het object, ga je `recognize_once()` de methode aanroepen. Met deze methode kan de spraakservice weten dat u één woordgroep verzendt voor herkenning en dat wanneer de woordgroep is geïdentificeerd om te stoppen met het herkennen van spraak.

Voeg deze code onder uw model in.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten weergeven (of fouten)

Wanneer het herkenningsresultaat wordt geretourneerd door de spraakservice, wilt u er iets mee doen. We houden het simpel en printen het resultaat af op de console.

Onder uw `recognize_once()`oproep om , voeg deze code.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Controleer uw code

Op dit punt moet je code er zo uitzien.

> [!NOTE]
> We hebben een aantal opmerkingen toegevoegd aan deze versie.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Voer het voorbeeld uit vanaf de console of in uw IDE:

```
python quickstart.py
```

De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
