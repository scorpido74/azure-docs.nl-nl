---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: 314617554abf8fee430e47eb4b0a0ca5db5bc75f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375409"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java Runtime <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) kunt initialiseren:

* Met een abonnement: geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken hoe een [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) wordt gemaakt met behulp van een sleutel en regio. Zie de pagina [regio-ondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden.

```java
SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Een herkenner initialiseren

Nadat u een [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) hebt gemaakt, is de volgende stap het initialiseren van een [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable). Wanneer u een [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) initialiseert, moet u er uw `config` aan doorgeven. Dit zorgt voor de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaardmicrofoon van uw apparaat, ziet u hier hoe de [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) eruit moet zien:

```java
SpeechRecognizer recognizer = new SpeechRecognizer(config);
```

Als u het audio-invoerapparaat wilt opgeven, moet u een [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) maken en de parameter `audioConfig` opgeven bij het initialiseren van uw [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable).

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Voeg eerst de volgende `import`-instructies toe.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;
```

Daarna kunt u als volgt verwijzen naar het `AudioConfig`-object:

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Als u een audiobestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig` opgeven. Wanneer u echter een [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable) maakt in plaats van `fromDefaultMicrophoneInput` aan te roepen, roept u `fromWavFileOutput` aan en geeft u de parameter `filename` door.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

## <a name="recognize-speech"></a>Spraak herkennen

De [Recognizer-klasse](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) voor de Speech SDK voor Java maakt een aantal methoden beschikbaar die u voor spraakherkenning kunt gebruiken.

* Eenmalige herkenning (asynchroon): voert herkenning uit in een niet-blokkerende (asynchrone) modus. Hiermee wordt één uiting herkend. Het einde van één uiting wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio is verwerkt.
* Continue herkenning (asynchroon): initieert asynchroon een continue herkenningsbewerking. Als u een audiobestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig` opgeven. Als u asynchrone continue herkenning wilt stoppen, roept u [stopContinuousRecognitionAsync](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) aan.

> [!NOTE]
> Meer informatie over het [kiezen van een spraakherkenningsmodus](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Eenmalige herkenning

Hier volgt een voor beeld van asynchrone eenmalige herkenning met [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable):

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

U moet code schrijven om het resultaat af te handelen. In dit voorbeeld wordt de [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable) geëvalueerd:

* Het herkenningsresultaat afdrukken: `ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst wordt gevonden, wordt de gebruiker hiervan op de hoogte gesteld: `ResultReason.NoMatch`
* Als er een fout is opgetreden wordt het foutbericht afgedrukt: `ResultReason.Canceled`

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

Continue herkenning is wat ingewikkelder dan eenmalige herkenning. U moet zich abonneren op de gebeurtenissen `recognizing`, `recognized`en `canceled` om de herkenningsresultaten op te halen. Als u de herkenning wilt stoppen, moet u [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)aanroepen. Hier volgt een voorbeeld van hoe doorlopende herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van de invoer en het initialiseren van een [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable):

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Vervolgens maken we een variabele voor het beheren van de status van spraakherkenning. We declareren een `Semaphore` in het klassebereik.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

We nemen een abonnement op de gebeurtenissen die vanuit de [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable) worden verzonden.

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable): Signaal voor gebeurtenissen met tussenliggende herkenningsresultaten.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (wat een geslaagde herkenningspoging aangeeft).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable): Signaal voor gebeurtenissen met een geannuleerde herkenningsresultaten (waarmee een herkenningspoging wordt aangegeven die is geannuleerd als gevolg van een rechtstreekse annuleringsaanvraag of van een transport- of protocolfout).

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

Nu alles is ingesteld, kunnen we [`startContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync) aanroepen.

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning kunt u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. In deze modus interpreteert de spraakconfiguratie-instantie woordbeschrijvingen van zinselementen zoals interpunctie. Bijvoorbeeld: de uiting 'Woon je hier in de stad vraagteken' wordt geïnterpreteerd als de tekst 'Woon je hier in de stad?'.

Als u de dicteermodus wilt inschakelen, gebruikt u de [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable)-methode op uw [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable).

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer- (of bron)taal. Laten we eens kijken hoe u de invoertaal wijzigt in Frans. Zoek uw [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable) in uw code en voeg deze regel daar direct onder toe.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable) is een parameter die een tekenreeks als argument gebruikt. U kunt elke waarde in de lijst met ondersteunde [landinstellingen/talen](../../../language-support.md) opgeven.

## <a name="improve-recognition-accuracy"></a>Nauwkeurigheid van de herkenning verbeteren

Er zijn een aantal manieren om de nauwkeurigheid van de herkenning te verbeteren met de Speech SDK. Neem bijvoorbeeld frasenlijsten. Frasenlijsten worden gebruikt om bekende frasen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Er kunnen losse woorden of hele frasen worden toegevoegd aan een frasenlijst. Tijdens de herkenning wordt een vermelding in een frasenlijst gebruikt als er een exacte overeenkomst voor de volledige frase is opgenomen in de audio. Als er geen exacte overeenkomst met de frase wordt gevonden, wordt de herkenning niet ondersteund.

> [!IMPORTANT]
> De frasenlijstfunctie is alleen beschikbaar in het Engels.

Als u een frasenlijst wilt gebruiken, maakt u eerst een [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable)-object, en voegt u vervolgens specifieke woorden en frasen toe met [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_).

Wijzigingen in [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable) worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraakservice.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Als u uw frasenlijst wilt wissen: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties voor het verbeteren van de nauwkeurigheid van de herkenning

Frasenlijsten zijn maar één optie om de nauwkeurigheid van de herkenning te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../how-to-custom-speech.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)
