---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 01/27/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3481d751aacd4183bf5a999422b9631ec0e21edb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925315"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre" target="_blank">Installeer de Speech SDK voor uw ontwikkelomgeving<span class="docon docon-navigate-external x-hidden-focus"></span>en maak een leeg voorbeeldproject.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor intentieherkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Uw project openen

1. Open uw favoriete IDE.
2. Laad uw project `Main.java`en open .

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Voordat u een `IntentRecognizer` object initialiseren, moet u een configuratie maken die de sleutel en locatie voor uw LUIS-voorspellingsbron gebruikt.  

Voeg deze code in het `main()`try/catch block in . Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door uw LUIS-voorspellingssleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door uw LUIS-locatie. **Regio-id** gebruiken vanuit [regio](https://aka.ms/speech/sdkregion)

>[!TIP]
> Zie [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition)als u hulp nodig hebt bij het vinden van deze waarden.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

Dit voorbeeld `FromSubscription()` gebruikt de `SpeechConfig`methode om de . Zie [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)voor een volledige lijst met beschikbare methoden.

De Spraak-SDK wordt standaard herkend door het gebruik van en-ons voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizeer initialiseren

Laten we nu een. `IntentRecognizer` Plaats deze code direct onder uw spraakconfiguratie.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en Intents toevoegen

U moet een `LanguageUnderstandingModel` gekoppeld aan de intentieherkenning en de gewenste intenties toevoegen die u wilt herkennen. We gaan intents uit het vooraf gebouwde domein gebruiken voor domotica.

Voeg deze code `IntentRecognizer`in onder uw . Zorg ervoor dat `"YourLanguageUnderstandingAppId"` u uw LUIS-app-id vervangt.

>[!TIP]
> Zie [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition)als u hulp nodig hebt bij het vinden van deze waarde.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Van `IntentRecognizer` het object, ga je `recognizeOnceAsync()` de methode aanroepen. Met deze methode kan de spraakservice weten dat u één woordgroep verzendt voor herkenning en dat wanneer de woordgroep is geïdentificeerd om te stoppen met het herkennen van spraak.

Voeg deze code onder uw model in:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten weergeven (of fouten)

Wanneer het herkenningsresultaat wordt geretourneerd door de spraakservice, wilt u er iets mee doen. We houden het simpel en printen het resultaat af op de console.

Voeg deze code in `recognizeOnceAsync()`onder uw oproep naar .

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Bronnen vrijgeven

Het is belangrijk om de spraakbronnen vrij te geven wanneer u klaar bent met het gebruik ervan. Voeg deze code in aan het einde van het try/catch block:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Controleer uw code

Op dit punt moet uw code er als volgt uitzien:

> [!NOTE]
> We hebben een aantal opmerkingen toegevoegd aan deze versie.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Druk op <kbd>F11</kbd>of selecteer **Foutopsporing** > **uitvoeren**.
De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
