---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 7d29f781950784cdb8951b3a6e45d5b5d0f9819f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79540104"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren. Gebruik afhankelijk van uw platform de volgende instructies:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de spraakservice wilt aanroepen met [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) hoe een wordt gemaakt met behulp van een sleutel en regio.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initialiseren van een herkenningspunt

Nadat u een [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable), de volgende stap is [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)het initialiseren van een . Wanneer u een [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)initialiseren, moet je het `config`doorgeven van uw . Dit biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) er als volgt uit:

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) u een `audioConfig` parameter maken [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Voeg eerst de `import` volgende instructies toe.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Vervolgens u als volgt `AudioConfig` naar het object verwijzen:

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audioConfig`te gebruiken, moet u nog steeds een . Wanneer u echter [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable)een - `fromDefaultMicrophoneInput`in plaats van `fromWavFileOutput` aanteroepen - maakt, belt u en geeft u de `filename` parameter door.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Spraak herkennen

De [klasse Recognizeer](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) voor de SpraakSDK voor Java legt een aantal methoden bloot die u gebruiken voor spraakherkenning.

* Single-shot herkenning (async) - Voert herkenning uit in een niet-blokkerende (asynchrone) modus. Dit herkent één utterance. Het einde van een enkele utterance wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio wordt verwerkt.
* Continue herkenning (async) - Asynchroon initieert continue herkenningsbewerking. Als u een audiobestand wilt verstrekken in plaats van een microfoon te gebruiken, moet u nog steeds een. Als u asynchrone continue herkenning wilt stoppen, belt u [ContinuousRecognitionAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync).

> [!NOTE]
> Meer informatie over het [kiezen van een spraakherkenningsmodus](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Single-shot herkenning

Hier is een voorbeeld van asynchrone [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable)single-shot herkenning met behulp van:

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

Je moet wat code schrijven om het resultaat te verwerken. In dit voorbeeld [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable)wordt de volgende beoordeling beoordeeld:

* Hiermee drukt u het herkenningsresultaat af:`ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst is, informeer de gebruiker:`ResultReason.NoMatch`
* Als er een fout is opgetreden, drukt u het foutbericht af:`ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>Continue herkenning

Continue herkenning is een beetje meer betrokken dan single-shot erkenning. U moet zich abonneren `recognizing` `recognized`op `canceled` de , , en gebeurtenissen om de erkenning resultaten te krijgen. Om de herkenning te [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)stoppen, moet u bellen. Hier is een voorbeeld van hoe continue herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)de input en het initialiseren van een:

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Laten we vervolgens een variabele maken om de status van spraakherkenning te beheren. Om te beginnen, verklaren `Semaphore` we een bij de klas scope.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

We abonneren ons op de [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)evenementen die vanuit de .

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): Signaal voor gebeurtenissen die tussentijdse herkenningsresultaten bevatten.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (met vermelding van een geslaagde herkenningspoging).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): Signaal voor gebeurtenissen die geannuleerde herkenningsresultaten bevatten (met vermelding van een herkenningspoging die als gevolg is geannuleerd of een directe annuleringsaanvraag of, als alternatief, een transport- of protocolfout).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Met alles opgezet, kunnen [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)we bellen.

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. Deze modus zorgt ervoor dat de instantie spraakconfig woordbeschrijvingen van zinsstructuren zoals interpunctie interpreteert. Bijvoorbeeld, de uiting "Woont u in de stad vraagteken" zou worden geïnterpreteerd als de tekst "Woont u in de stad?".

Als u de dicteermodus [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable) wilt inschakelen, gebruikt u de methode op uw [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal in het Frans zou veranderen. In uw code, [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)vind je , voeg dan deze regel direct eronder.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable)is een parameter die een tekenreeks als argument neemt. U elke waarde in de lijst met ondersteunde [landlocaties/talen](../../../language-support.md)vermelden.

## <a name="improve-recognition-accuracy"></a>De herkenningsnauwkeurigheid verbeteren

Er zijn een paar manieren om de herkenningsnauwkeurigheid met de Speech SDK te verbeteren. Laten we eens kijken naar phrase lists. Woordgroeplijsten worden gebruikt om bekende zinnen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Enkele woorden of volledige woordgroepen kunnen worden toegevoegd aan een woordgroeplijst. Tijdens de herkenning wordt een vermelding in een woordenlijst gebruikt als een exacte overeenkomst voor de hele woordgroep in de audio is opgenomen. Als er geen exacte overeenkomst wordt gevonden met de woordgroep, wordt de herkenning niet ondersteund.

> [!IMPORTANT]
> De functie Lijst met zinnen is alleen beschikbaar in het Engels.

Als u een woordgroeplijst [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) wilt gebruiken, maakt u [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_)eerst een object en voegt u vervolgens specifieke woorden en zinnen toe met .

Wijzigingen die [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) van kracht worden op de volgende herkenning of na een heraansluiting met de spraakservice.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Als u uw woordenlijst moet wissen: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties om de herkenningsnauwkeurigheid te verbeteren

Woordlijsten zijn slechts één optie om de herkenningsnauwkeurigheid te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../how-to-custom-speech.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)