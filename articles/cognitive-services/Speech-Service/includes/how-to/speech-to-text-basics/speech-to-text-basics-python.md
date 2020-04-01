---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: a0746ec73b827756ff47c0dfc010b37ed2b79844
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80502374"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt uitgegaan van:

* U hebt een Azure-account en een abonnement op de Spraakservice. Als u geen account en abonnement hebt en een abonnement : [Probeer de spraakservice gratis.](../../../get-started.md)

## <a name="install-and-import-the-speech-sdk"></a>De Spraak-SDK installeren en importeren

Voordat u iets doen, moet u de Speech SDK installeren.

```Python
pip install azure-cognitiveservices-speech
```

Als u macOS bevindt en installatieproblemen ondervindt, moet u deze opdracht mogelijk eerst uitvoeren.

```Python
python3 -m pip install --upgrade pip
```

Nadat de SpraakSDK is geïnstalleerd, importeert u deze in uw Python-project met deze instructie.

```Python
import azure.cognitiveservices.speech as speechsdk
```

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de spraakservice wilt aanroepen met [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) hoe een wordt gemaakt met behulp van een sleutel en regio. Zie de [pagina regioondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden.

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

## <a name="initialize-a-recognizer"></a>Initialiseren van een herkenningspunt

Nadat u een [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python), de volgende stap is [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)het initialiseren van een . Wanneer u een [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)initialiseren, moet je het `speech_config`doorgeven van uw . Dit biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python) er als volgt uit:

```Python
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config)
```

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python) u een `audio_config` parameter maken [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

```Python
audio_config = AudioConfig(device_name="<device id>");
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audio_config`te gebruiken, moet u nog steeds een . Wanneer u echter [`AudioConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python)een , in `device_name`plaats van het `filename` verstrekken van de , gebruik je de parameter.

```Python
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)
```

## <a name="recognize-speech"></a>Spraak herkennen

De [klasse Recognizeer](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python) voor de SpraakSDK voor Python onthult een aantal methoden die u gebruiken voor spraakherkenning.

* Single-shot herkenning (synchronisatie) - Voert herkenning uit in een blokkerende (synchrone) modus. Retouren nadat één utterance is herkend. Het einde van een enkele utterance wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio wordt verwerkt. De taak retourneert de herkenningstekst als resultaat.
* Single-shot herkenning (async) - Voert herkenning uit in een niet-blokkerende (asynchrone) modus. Dit herkent één utterance. Het einde van een enkele utterance wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio wordt verwerkt.
* Continue herkenning (synchronisatie) - Start synchroon continue herkenning. De client moet `EventSignal` verbinding maken om herkenningsresultaten te ontvangen. Als u de herkenning wilt stoppen, belt u [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--).
* Continue herkenning (async) - Asynchroon initieert continue herkenningsbewerking. De gebruiker moet verbinding maken met EventSignal om herkenningsresultaten te ontvangen. Als u asynchrone continue herkenning wilt stoppen, roept [u stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)aan.

> [!NOTE]
> Meer informatie over het [kiezen van een spraakherkenningsmodus](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Single-shot herkenning

Hier is een voorbeeld van synchrone [`recognize_once()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once)single-shot herkenning met behulp van:

```Python
result = speech_recognizer.recognize_once()
```

Hier is een voorbeeld van synchrone [`recognize_once_async()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognize-once-async------azure-cognitiveservices-speech-resultfuture)single-shot herkenning met behulp van:

```Python
result = speech_recognizer.recognize_once_async()
```

Ongeacht of u de synchrone of asynchrone methode hebt gebruikt, moet u een code schrijven om het resultaat te herhalen. In dit voorbeeld [`result.reason`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.resultreason?view=azure-python)wordt de volgende beoordeling beoordeeld:

* Hiermee drukt u het herkenningsresultaat af:`speechsdk.ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst is, informeer de gebruiker:`speechsdk.ResultReason.NoMatch `
* Als er een fout is opgetreden, drukt u het foutbericht af:`speechsdk.ResultReason.Canceled`

```Python
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="continuous-recognition"></a>Continue herkenning

Continue herkenning is een beetje meer betrokken dan single-shot erkenning. Het vereist dat u `EventSignal` verbinding maakt met de om de herkenningsresultaten te krijgen, en om de herkenning te stoppen, moet u [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) of [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition-async--)bellen. Hier is een voorbeeld van hoe continue herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python)de input en het initialiseren van een:

```Python
audio_config = speechsdk.audio.AudioConfig(filename=weatherfilename)
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_config)
```

Laten we vervolgens een variabele maken om de status van spraakherkenning te beheren. Om te beginnen stellen we `False`dit in op , want aan het begin van de erkenning kunnen we er veilig van uitgaan dat het nog niet klaar is.

```Python
done = False
```

Nu gaan we een callback maken om continue `evt` herkenning te stoppen wanneer een ontvangen wordt. Er zijn een paar dingen om in gedachten te houden.

* Wanneer `evt` een bericht `evt` wordt ontvangen, wordt het bericht afgedrukt.
* Nadat `evt` een ontvangen is, wordt [stop_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#stop-continuous-recognition--) opgeroepen om de herkenning te stoppen.
* De herkenningsstatus `True`wordt gewijzigd in .

```Python
def stop_cb(evt):
    print('CLOSING on {}'.format(evt))
    speech_recognizer.stop_continuous_recognition()
    nonlocal done
    done = True
```

In dit codevoorbeeld ziet u hoe u [`SpeechRecognizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#start-continuous-recognition--)terugbellen koppelen aan gebeurtenissen die vanuit de.

* [`recognizing`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognizing): Signaal voor gebeurtenissen die tussentijdse herkenningsresultaten bevatten.
* [`recognized`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#recognized): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (met vermelding van een geslaagde herkenningspoging).
* [`session_started`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-started): Signaal voor gebeurtenissen die het begin van een herkenningssessie (bewerking) aangeven.
* [`session_stopped`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`canceled`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#canceled): Signaal voor gebeurtenissen die geannuleerde herkenningsresultaten bevatten (met vermelding van een herkenningspoging die als gevolg is geannuleerd of een directe annuleringsaanvraag of, als alternatief, een transport- of protocolfout).

```Python
speech_recognizer.recognizing.connect(lambda evt: print('RECOGNIZING: {}'.format(evt)))
speech_recognizer.recognized.connect(lambda evt: print('RECOGNIZED: {}'.format(evt)))
speech_recognizer.session_started.connect(lambda evt: print('SESSION STARTED: {}'.format(evt)))
speech_recognizer.session_stopped.connect(lambda evt: print('SESSION STOPPED {}'.format(evt)))
speech_recognizer.canceled.connect(lambda evt: print('CANCELED {}'.format(evt)))

speech_recognizer.session_stopped.connect(stop_cb)
speech_recognizer.canceled.connect(stop_cb)
```

Met alles ingesteld, kunnen we bellen [met start_continuous_recognition()](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.recognizer?view=azure-python#session-stopped).

```Python
speech_recognizer.start_continuous_recognition()
while not done:
    time.sleep(.5)
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. Deze modus zorgt ervoor dat de instantie spraakconfig woordbeschrijvingen van zinsstructuren zoals interpunctie interpreteert. Bijvoorbeeld, de uiting "Woont u in de stad vraagteken" zou worden geïnterpreteerd als de tekst "Woont u in de stad?".

Als u de dicteermodus [`enable_dictation()`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--) wilt inschakelen, gebruikt u de methode op uw [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python).

```Python 
SpeechConfig.enable_dictation()
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal zou veranderen in het Duits. Zoek in uw code uw SpeechConfig en voeg deze regel er direct onder toe.

```Python
speech_config.speech_recognition_language="de-DE"
```

[`speech_recognition_language`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-recognition-language)is een parameter die een tekenreeks als argument neemt. U elke waarde in de lijst met ondersteunde [landlocaties/talen](../../../language-support.md)vermelden.

## <a name="improve-recognition-accuracy"></a>De herkenningsnauwkeurigheid verbeteren

Er zijn een paar manieren om de herkenningsnauwkeurigheid met de Speech SDK te verbeteren. Laten we eens kijken naar phrase lists. Woordgroeplijsten worden gebruikt om bekende zinnen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Enkele woorden of volledige woordgroepen kunnen worden toegevoegd aan een woordgroeplijst. Tijdens de herkenning wordt een vermelding in een woordenlijst gebruikt als een exacte overeenkomst voor de hele woordgroep in de audio is opgenomen. Als er geen exacte overeenkomst wordt gevonden met de woordgroep, wordt de herkenning niet ondersteund.

> [!IMPORTANT]
> De functie Lijst met zinnen is alleen beschikbaar in het Engels.

Als u een woordgroeplijst [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) wilt gebruiken, maakt u [`addPhrase`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python#addphrase-phrase--str-)eerst een object en voegt u vervolgens specifieke woorden en zinnen toe met .

Wijzigingen die [`PhraseListGrammar`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.phraselistgrammar?view=azure-python) van kracht worden op de volgende herkenning of na een heraansluiting met de spraakservice.

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Supercalifragilisticexpialidocious")
```

Als u uw woordenlijst moet wissen: 

```Python
phrase_list_grammar.clear()
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties om de herkenningsnauwkeurigheid te verbeteren

Woordlijsten zijn slechts één optie om de herkenningsnauwkeurigheid te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../how-to-custom-speech.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)