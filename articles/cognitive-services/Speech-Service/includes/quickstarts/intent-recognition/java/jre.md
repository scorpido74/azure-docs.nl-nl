---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 26b3b3bd061d3ec7c3efd5c613c5beec33867022
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87298795"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java" target="_blank">Installeer de Speech-SDK voor uw ontwikkelomgeving en maak een leeg voorbeeldproject<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor intentieherkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Open uw project

1. Open de IDE van uw voorkeur.
2. Laad uw project en open `Main.java`.

## <a name="start-with-some-boilerplate-code"></a>Beginnen met standaardcode

We gaan wat code toevoegen die als basis voor het project gaat dienen.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-20,68-75)]

## <a name="create-a-speech-configuration"></a>Een Speech-configuratie maken

Voordat u een `IntentRecognizer`-object kunt initialiseren, moet u een configuratie maken die gebruikmaakt van de sleutel en de locatie voor uw LUIS-voorspellingsresource.  

Voeg deze code toe aan het blok try/catch in `main()`. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door de LUIS-voorspellingssleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door de locatie van uw LUIS. Gebruik **Regio-id** uit [regio](https://aka.ms/speech/sdkregion)

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=27)]

In dit voorbeeld wordt de methode `FromSubscription()` gebruikt om de `SpeechConfig` te maken. Zie [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) voor een volledige lijst met beschikbare methoden.

De Speech-SDK probeert taal standaard te herkennen in en-US. Zie [De brontaal voor spraak-naar-tekst opgeven](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Laten we nu een `IntentRecognizer` maken. Voeg deze code toe onder uw Speech-configuratie.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=30)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en Intents toevoegen

U moet een `LanguageUnderstandingModel` koppelen aan de intentieherkenning en de intenties toevoegen die u wilt laten herkennen. We gaan de intenties van het vooraf ontwikkelde domein voor woningautomatisering gebruiken.

Voeg deze code toe onder uw `IntentRecognizer`. Zorg ervoor dat u `"YourLanguageUnderstandingAppId"` vervangt door de id van uw LUIS-app.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition).

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=33-35)]

In dit voorbeeld wordt de functie `addIntent()` gebruikt om intenties afzonderlijk toe te voegen. Als u alle intenties uit een model wilt toevoegen, gebruikt u `addAllIntents(model)` en geeft u het model door.

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer`-object roept u de methode `recognizeOnceAsync()` aan. Met deze methode laat u de Speech-service weten dat u één woordgroep verstuurt voor herkenning en dat er kan worden gestopt met het herkennen van spraak zodra de woordgroep is geïdentificeerd.

Voeg deze code toe onder uw model:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=40)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten (of fouten) weergeven

Wanneer het herkenningsresultaat wordt geretourneerd door de Speech-service, wilt u daar iets mee doen. We zullen het eenvoudig houden en het resultaat afdrukken naar de console.

Voeg deze code toe onder uw aanroep van `recognizeOnceAsync()`.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=43-64)]

## <a name="release-resources"></a>Resources vrijgeven

Het is belangrijk om de spraakresources vrij te geven wanneer u klaar bent met het gebruik ervan. Voeg deze code toe aan het einde van het blok try/catch:

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=66-67)]

## <a name="check-your-code"></a>Uw code controleren

Op dit punt moet uw code er als volgt uitzien:

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/intent-recognition/src/speechsdk/quickstart/Main.java?range=6-75)]

## <a name="build-and-run-your-app"></a>De app bouwen en uitvoeren

Druk op <kbd>F11</kbd> of selecteer **Uitvoeren** > **Fouten opsporen**.
De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
