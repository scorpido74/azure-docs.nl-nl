---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: abd69a49659ef745f81a2186ea97440cf35f20df
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671075"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">Installeer de Speech SDK voor uw ontwikkelomgeving<span class="docon docon-navigate-external x-hidden-focus"></span>en maak een leeg voorbeeldproject.</a>

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor intentieherkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

Open vervolgens uw project in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project `helloworld.cpp`en open .

## <a name="start-with-some-boilerplate-code"></a>Begin met een soort boilerplate-code

Laten we wat code toevoegen die werkt als een skelet voor ons project. Houd er rekening mee dat u `recognizeIntent()`een async-methode hebt gemaakt met de naam .

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Voordat u een `IntentRecognizer` object initialiseren, moet u een configuratie maken die de sleutel en locatie voor uw LUIS-voorspellingsbron gebruikt.

> [!IMPORTANT]
> Uw startsleutel en authoring keys werken niet. U moet uw voorspellingssleutel en locatie gebruiken die u eerder hebt gemaakt. Zie [Een LUIS-app maken voor intentieherkenning voor](#create-a-luis-app-for-intent-recognition)meer informatie.

Voeg deze code `recognizeIntent()` in de methode in. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door uw LUIS-voorspellingssleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door uw LUIS-locatie.  **Regio-id** gebruiken vanuit [regio](https://aka.ms/speech/sdkregion).

>[!TIP]
> Zie [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition)als u hulp nodig hebt bij het vinden van deze waarden.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

Dit voorbeeld `FromSubscription()` gebruikt de `SpeechConfig`methode om de . Zie [SpeechConfig Class](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)voor een volledige lijst met beschikbare methoden.

De Spraak-SDK wordt standaard herkend door het gebruik van en-ons voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizeer initialiseren

Laten we nu een. `IntentRecognizer` Voeg deze code `recognizeIntent()` in de methode in, direct onder uw spraakconfiguratie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en Intents toevoegen

U moet een `LanguageUnderstandingModel` gekoppeld aan de intentieherkenning en de gewenste intenties toevoegen die u wilt herkennen. We gaan intents uit het vooraf gebouwde domein gebruiken voor domotica.

Voeg deze code `IntentRecognizer`in onder uw . Zorg ervoor dat `"YourLanguageUnderstandingAppId"` u uw LUIS-app-id vervangt.

>[!TIP]
> Zie [Een LUIS-app maken voor intentieherkenning](#create-a-luis-app-for-intent-recognition)als u hulp nodig hebt bij het vinden van deze waarde.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Van `IntentRecognizer` het object, ga je `RecognizeOnceAsync()` de methode aanroepen. Met deze methode kan de spraakservice weten dat u één woordgroep verzendt voor herkenning en dat wanneer de woordgroep is geïdentificeerd om te stoppen met het herkennen van spraak. Voor de eenvoud zullen we wachten op de toekomst terug te voltooien.

Voeg deze code onder uw model in:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>De herkenningsresultaten weergeven (of fouten)

Wanneer het herkenningsresultaat wordt geretourneerd door de spraakservice, wilt u er iets mee doen. We houden het simpel en printen het resultaat af op de console.

Voeg deze `auto result = recognizer->RecognizeOnceAsync().get();`code hieronder in:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Controleer uw code

Op dit punt moet uw code er als volgt uitzien:

> [!NOTE]
> We hebben een aantal opmerkingen toegevoegd aan deze versie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en onze spraakherkenning te testen met behulp van de Spraakservice.

1. **Compileer de code** - Kies op de menubalk van Visual Studio **Build** > **Solution**.
2. **Start uw app** - Kies op de menubalk Debug**genfout opsporing van** **foutopsporing debuggen** > of druk op <kbd>F5</kbd>.
3. **Start herkenning** - Het zal u vragen om een zin in het Engels te spreken. Uw toespraak wordt verzonden naar de spraakservice, getranscribeerd als tekst en weergegeven in de console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
