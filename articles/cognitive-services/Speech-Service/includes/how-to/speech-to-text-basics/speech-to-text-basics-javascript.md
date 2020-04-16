---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: b11194640c4d049c90f85974022908dce6b4fd79
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399804"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript Speech SDK <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>installeren. Gebruik afhankelijk van uw platform de volgende instructies:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Knooppunt.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Bovendien, afhankelijk van de doelomgeving gebruik maken van een van de volgende:

# <a name="import"></a>[Importeren](#tab/import)

```javascript
import {
    AudioConfig,
    CancellationDetails,
    CancellationReason,
    PhraseListGrammar,
    ResultReason,
    SpeechConfig,
    SpeechRecognizer
} from "microsoft-cognitiveservices-speech-sdk";
```

Zie export `import`en import voor meer informatie over , zie <a href="https://javascript.info/import-export" target="_blank">export en import <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Vereisen](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Voor meer `require`informatie over , zie <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">wat nodig is? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[uit](#tab/script)

Download en win het <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> *microsoft.cognitiveservices.speech.bundle.js-bestand* en plaats deze in een map die toegankelijk is voor uw HTML-bestand.

```html
<script src="microsoft.cognitiveservices.speech.bundle.js"></script>;
```

> [!TIP]
> Als u een webbrowser target en `<script>` de tag gebruikt. het `sdk` voorvoegsel is niet nodig. Het `sdk` voorvoegsel is een `require` alias die wordt gebruikt om de module een naam te geven.

---

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de spraakservice wilt aanroepen met [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) hoe een wordt gemaakt met behulp van een sleutel en regio. Zie de [pagina regioondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden.

```javascript
const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initialiseren van een herkenningspunt

Nadat u een [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest), de volgende stap is [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)het initialiseren van een . Wanneer u een [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)initialiseren, moet je het `speechConfig`doorgeven van uw . Dit biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) er als volgt uit:

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) u een `audioConfig` parameter maken [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Als `AudioConfig` volgt naar het object verwijzen:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const speechConfig = SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audioConfig`te gebruiken, moet u nog steeds een . Dit kan echter alleen bij het targeten van **Node.js** en wanneer u [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest)een - in plaats van aanteroepen `fromDefaultMicrophoneInput`- maakt, belt `fromWavFileOutput` u en geeft u de `filename` parameter door.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const speechConfig = SpeechConfig.fromSubscription(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Spraak herkennen

De [klasse Recognizeer](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest) voor de SpraakSDK voor C# legt een aantal methoden bloot die u gebruiken voor spraakherkenning.

* Single-shot herkenning (async) - Voert herkenning uit in een niet-blokkerende (asynchrone) modus. Dit herkent één utterance. Het einde van een enkele utterance wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio wordt verwerkt.
* Continue herkenning (async) - Asynchroon initieert continue herkenningsbewerking. De gebruiker registreert zich bij gebeurtenissen en verwerkt verschillende toepassingsstatus. Als u asynchrone [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)continue herkenning wilt stoppen, roept u .

> [!NOTE]
> Meer informatie over het [kiezen van een spraakherkenningsmodus](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Single-shot herkenning

Hier is een voorbeeld van asynchrone [`recognizeOnceAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync)single-shot herkenning met behulp van:

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Je moet wat code schrijven om het resultaat te verwerken. In dit voorbeeld [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest#reason)wordt de volgende beoordeling beoordeeld:

* Hiermee drukt u het herkenningsresultaat af:`ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst is, informeer de gebruiker:`ResultReason.NoMatch`
* Als er een fout is opgetreden, drukt u het foutbericht af:`ResultReason.Canceled`

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        console.log("    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
}
```

### <a name="continuous-recognition"></a>Continue herkenning

Continue herkenning is een beetje meer betrokken dan single-shot erkenning. U moet zich abonneren `Recognizing` `Recognized`op `Canceled` de , , en gebeurtenissen om de erkenning resultaten te krijgen. Om de herkenning te [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)stoppen, moet u bellen. Hier is een voorbeeld van hoe continue herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)de input en het initialiseren van een:

```javascript
const recognizer = new SpeechRecognizer(speechConfig);
```

We abonneren ons op de [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest)evenementen die vanuit de .

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizing): Signaal voor gebeurtenissen die tussentijdse herkenningsresultaten bevatten.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognized): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (met vermelding van een geslaagde herkenningspoging).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#sessionstopped): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#canceled): Signaal voor gebeurtenissen die geannuleerde herkenningsresultaten bevatten (met vermelding van een herkenningspoging die als gevolg is geannuleerd of een directe annuleringsaanvraag of, als alternatief, een transport- of protocolfout).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Met alles opgezet, kunnen [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#stopcontinuousrecognitionasync)we bellen.

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();

// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. Deze modus zorgt ervoor dat de instantie spraakconfig woordbeschrijvingen van zinsstructuren zoals interpunctie interpreteert. Bijvoorbeeld, de uiting "Woont u in de stad vraagteken" zou worden geïnterpreteerd als de tekst "Woont u in de stad?".

Als u de dicteermodus [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#enabledictation--) wilt inschakelen, gebruikt u de methode op uw [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest).

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal in het Italiaans zou veranderen. In uw code, [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)vind je , voeg dan deze regel direct eronder.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

De [`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#speechrecognitionlanguage) accommodatie verwacht een tekenreeks voor taal-landformaat. U elke waarde in de kolom **Locale** vermelden in de lijst met ondersteunde [landlocaties/talen.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>De herkenningsnauwkeurigheid verbeteren

Er zijn een paar manieren om de herkenningsnauwkeurigheid te verbeteren met de spraaklijsten Laten we eens kijken naar Woordlijsten. Woordgroeplijsten worden gebruikt om bekende zinnen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Enkele woorden of volledige woordgroepen kunnen worden toegevoegd aan een woordgroeplijst. Tijdens de herkenning wordt een vermelding in een woordenlijst gebruikt als een exacte overeenkomst voor de hele woordgroep in de audio is opgenomen. Als er geen exacte overeenkomst wordt gevonden met de woordgroep, wordt de herkenning niet ondersteund.

> [!IMPORTANT]
> De functie Lijst met zinnen is alleen beschikbaar in het Engels.

Als u een woordgroeplijst [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) wilt gebruiken, maakt u [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest#addphrase-string-)eerst een object en voegt u vervolgens specifieke woorden en zinnen toe met .

Wijzigingen die [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest) van kracht worden op de volgende herkenning of na een heraansluiting met de spraakservice.

```javascript
const phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Als u uw woordenlijst moet wissen:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties om de herkenningsnauwkeurigheid te verbeteren

Woordlijsten zijn slechts één optie om de herkenningsnauwkeurigheid te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../how-to-custom-speech.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)