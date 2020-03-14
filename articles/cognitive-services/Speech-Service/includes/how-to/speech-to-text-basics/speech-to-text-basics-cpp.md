---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: dapine
ms.openlocfilehash: 8392e03f272c6db2bb384c8031cb975bca53d530
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372800"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Spreek<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Als u de spraak service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eind punt, de host of het autorisatie token.

> [!NOTE]
> Ongeacht of u spraak herkenning, spraak synthese, vertaling of intentie herkenning uitvoert, maakt u altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)kunt initialiseren:

* Met een abonnement: Geef een sleutel en de bijbehorende regio door.
* Met een eind punt: Pass in een speech service-eind punt. Een sleutel-of autorisatie token is optioneel.
* Met een host: Geef een hostadres door. Een sleutel-of autorisatie token is optioneel.
* Met een autorisatie token: Geef een autorisatie token en de bijbehorende regio door.

Laten we eens kijken hoe een [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) is gemaakt met behulp van een sleutel en regio.

```cpp
auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Een herkenner initialiseren

Nadat u een [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)hebt gemaakt, is de volgende stap het initialiseren van een [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer). Wanneer u een [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)initialiseert, moet u de `speech_config`door geven. Dit biedt de referenties die de speech-service nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaard microfoon van uw apparaat, ziet u hier de [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) eruit moet zien:

```cpp
auto recognizer = SpeechRecognizer::FromConfig(config);
```

Als u het audio-invoer apparaat wilt opgeven, moet u een [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) maken en de `audioConfig`-para meter opgeven bij het initialiseren van uw [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer).

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het apparaat voor audio-invoer](../../../how-to-select-audio-input-devices.md).

Voeg eerst de volgende `using namespace`-instructie toe na uw `#include` definities.

```cpp
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

Daarna kunt u als volgt verwijzen naar het `AudioConfig`-object:

```cpp
auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
```

Als u een audio bestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig`opgeven. Wanneer u echter een [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig)maakt in plaats van `FromDefaultMicrophoneInput`aan te roepen, roept u `FromWavFileOutput` aan en geeft u de para meter `filename` door.

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

## <a name="recognize-speech"></a>Spraak herkennen

De [klasse Recognizer](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer) voor de Speech SDK voor C++ beschrijft een aantal methoden die u voor spraak herkenning kunt gebruiken.

* Eenmalige herkenning (asynchroon): voert herkenning uit in een niet-blokkerende (asynchrone) modus. Hiermee wordt één utterance herkend. Het einde van één utterance wordt bepaald door te Luis teren naar stilte aan het einde of tot een maximum van 15 seconden audio wordt verwerkt.
* Continue herkenning (asynchroon): initieert asynchrone herkennings bewerking. De gebruiker moet verbinding maken met de afhandelings gebeurtenis om herkennings resultaten te ontvangen. Roep [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)aan om asynchrone voortdurende herkenning te stoppen.

> [!NOTE]
> Meer informatie over het [kiezen van een modus voor spraak herkenning](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Eenmalige herkenning

Hier volgt een voor beeld van asynchrone eenmalige herkenning met [`RecognizeOnceAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync):

```cpp
auto result = recognizer->RecognizeOnceAsync().get();
```

U moet een code schrijven om het resultaat te kunnen afhandelen. In dit voor beeld wordt het [`result->Reason`](https://docs.microsoft.com/cpp/cognitive-services/speech/recognitionresult#reason)geëvalueerd:

* Het herkennings resultaat afdrukken: `ResultReason::RecognizedSpeech`
* Als er geen overeenkomst wordt gevonden, stelt u de gebruiker hiervan op de hoogte: `ResultReason::NoMatch`
* Als er een fout is opgetreden, drukt u het fout bericht af: `ResultReason::Canceled`

```cpp
switch (result->Reason)
{
    case ResultReason::RecognizedSpeech:
        cout << "We recognized: " << result->Text << std::endl;
        break;
    case ResultReason::NoMatch:
        cout << "NOMATCH: Speech could not be recognized." << std::endl;
        break;
    case ResultReason::Canceled:
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;
    
            if (cancellation->Reason == CancellationReason::Error) {
                cout << "CANCELED: ErrorCode= " << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
        break;
    default:
        break;
}
```

### <a name="continuous-recognition"></a>Doorlopende herkenning

Doorlopende herkenning is een beetje meer betrokken dan eenmalige herkenning. U moet zich abonneren op de `Recognizing`, `Recognized`en `Canceled` gebeurtenissen om de herkennings resultaten op te halen. Als u de herkenning wilt stoppen, moet u [StopContinuousRecognitionAsync](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#stopcontinuousrecognitionasync)aanroepen. Hier volgt een voor beeld van hoe doorlopende herkenning wordt uitgevoerd op een audio-invoer bestand.

Laten we beginnen met het definiëren van de invoer en het initialiseren van een [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer):

```cpp
auto audioInput = AudioConfig::FromWavFileInput("YourAudioFile.wav");
auto recognizer = SpeechRecognizer::FromConfig(config, audioInput);
```

Vervolgens maken we een variabele voor het beheren van de status van spraak herkenning. Om te beginnen, declareren we een `promise<void>`, aangezien we bij het begin van de herkenning veilig kunnen aannemen dat het nog niet is voltooid.

```cpp
promise<void> recognitionEnd;
```

Er wordt een abonnement genomen op de gebeurtenissen die vanuit de [`SpeechRecognizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer)worden verzonden.

* [`Recognizing`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizing): signaal voor gebeurtenissen met tussenliggende herkennings resultaten.
* [`Recognized`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognized): signaal voor gebeurtenissen met de uiteindelijke herkennings resultaten (wat een geslaagde herkennings poging aangeeft).
* [`SessionStopped`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#sessionstopped): signaal voor gebeurtenissen die het einde van een herkennings sessie (bewerking) aangeven.
* [`Canceled`](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#canceled): signaal voor gebeurtenissen met een geannuleerde herkennings resultaten (waarmee een herkennings poging wordt aangegeven die is geannuleerd als gevolg van een aanvraag of een rechtstreekse annulering of een Trans Port-of protocol fout).

```cpp
recognizer->Recognizing.Connect([](const SpeechRecognitionEventArgs& e)
    {
        cout << "Recognizing:" << e.Result->Text << std::endl;
    });

recognizer->Recognized.Connect([](const SpeechRecognitionEventArgs& e)
    {
        if (e.Result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << e.Result->Text 
                 << " (text could not be translated)" << std::endl;
        }
        else if (e.Result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
    });

recognizer->Canceled.Connect([&recognitionEnd](const SpeechRecognitionCanceledEventArgs& e)
    {
        cout << "CANCELED: Reason=" << (int)e.Reason << std::endl;
        if (e.Reason == CancellationReason::Error)
        {
            cout << "CANCELED: ErrorCode=" << (int)e.ErrorCode << "\n"
                 << "CANCELED: ErrorDetails=" << e.ErrorDetails << "\n"
                 << "CANCELED: Did you update the subscription info?" << std::endl;

            recognitionEnd.set_value(); // Notify to stop recognition.
        }
    });

recognizer->SessionStopped.Connect([&recognitionEnd](const SessionEventArgs& e)
    {
        cout << "Session stopped.";
        recognitionEnd.set_value(); // Notify to stop recognition.
    });
```

Als alles is ingesteld, kunnen we [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechrecognizer#startcontinuousrecognitionasync)aanroepen.

```cpp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer->StartContinuousRecognitionAsync().get();

// Waits for recognition end.
recognitionEnd.get_future().get();

// Stops recognition.
recognizer->StopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Dicteer modus

Bij het gebruik van doorlopende herkenning kunt u de verwerking van spraak herkenning inschakelen met behulp van de bijbehorende functie ' dicteren inschakelen '. Deze modus zorgt ervoor dat het spraak configuratie-exemplaar Word-beschrijvingen interpreteert, zoals Lees tekens. Bijvoorbeeld: het utterance ' hebt u Live in het stads vraag teken ' zou worden geïnterpreteerd als de tekst ' wilt u in de stad wonen? '.

Als u de dicteer modus wilt inschakelen, gebruikt u de [`EnableDictation`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation) methode op uw [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig).

```cpp
config->EnableDictation();
```

## <a name="change-source-language"></a>Bron taal wijzigen

Een veelvoorkomende taak voor spraak herkenning is het opgeven van de invoer-(of bron-) taal. Laten we eens kijken hoe u de invoer taal in het Duits wijzigt. Zoek uw [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)in uw code en voeg deze regel vervolgens direct onder toe.

```cpp
config->SetSpeechRecognitionLanguage("fr-FR");
```

[`SetSpeechRecognitionLanguage`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage) is een para meter die een teken reeks als argument gebruikt. U kunt een wille keurige waarde opgeven in de lijst met ondersteunde [land instellingen/talen](../../../language-support.md).

## <a name="improve-recognition-accuracy"></a>Nauw keurigheid van herkenning verbeteren

Er zijn een aantal manieren om de nauw keurigheid van de herkenning te verbeteren met de spraak-SDK. Laten we eens kijken naar woordgroepen lijsten. Woordgroepen lijsten worden gebruikt voor het identificeren van bekende woord groepen in audio gegevens, zoals de naam van een persoon of een specifieke locatie. Enkele woorden of volledige woord groepen kunnen worden toegevoegd aan een woordgroepen lijst. Tijdens de herkenning wordt een vermelding in een woordgroepen lijst gebruikt als er een exacte overeenkomst voor de volledige woord groep wordt opgenomen in de audio. Als een exacte overeenkomst met de zin niet wordt gevonden, wordt de herkenning niet ondersteund.

> [!IMPORTANT]
> De functie woordgroepen lijst is alleen beschikbaar in het Engels.

Als u een woordgroepen lijst wilt gebruiken, maakt u eerst een [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) -object en voegt u vervolgens specifieke woorden en zinsdelen toe met [`AddPhrase`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar#addphrase).

Wijzigingen in [`PhraseListGrammar`](https://docs.microsoft.com/cpp/cognitive-services/speech/phraselistgrammar) van kracht worden bij de volgende herkenning of na het opnieuw verbinden met de spraak service.

```cpp
auto phraseListGrammar = PhraseListGrammar::FromRecognizer(recognizer);
phraseListGrammar->AddPhrase("Supercalifragilisticexpialidocious");
```

Als u uw woordgroepen lijst wilt wissen: 

```cpp
phraseListGrammar->Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties om de nauw keurigheid van de herkenning te verbeteren

Woordgroepen lijsten zijn slechts één optie om de nauw keurigheid van de herkenning te verbeteren. U kunt ook: 

* [Nauw keurigheid verbeteren met Custom Speech](../../../how-to-custom-speech.md)
* [Nauw keurigheid verbeteren met Tenant modellen](../../../tutorial-tenant-model.md)