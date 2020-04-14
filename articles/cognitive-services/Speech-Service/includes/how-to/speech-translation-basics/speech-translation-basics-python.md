---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: cd7a8c0104a903f49c5ce8ae1e1a561d53c0fbf5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266634"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies onder het gedeelte <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> van het Speech SDK-artikel.

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit `import` artikel wilt uitvoeren, neemt u de volgende instructies op boven aan het python-codebestand.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevingsvariabelen

De voorbeeldbroncode in dit artikel is afhankelijk van omgevingsvariabelen voor het opslaan van gevoelige gegevens, zoals de abonnementssleutel voor spraakbronnen en regio. Het python-codebestand bevat twee waarden die zijn toegewezen `SPEECH__SUBSCRIPTION__KEY` aan `SPEECH__SERVICE__REGION`de omgevingsvariabelen van de hostmachines, namelijk en . Beide variabelen bevinden zich op het globale bereik, waardoor ze toegankelijk zijn binnen de functiedefinitie van het codebestand. Zie [omgevingsvariabelen en toepassingsconfiguratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevingsvariabelen.

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Een configuratie van spraakvertaling maken

Als u de spraakservice wilt aanroepen met [`SpeechTranslationConfig`][config]de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!TIP]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechTranslationConfig`][config]u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken [`SpeechTranslationConfig`][config] hoe een wordt gemaakt met behulp van een sleutel en regio. Zie de [pagina regioondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden.

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakvertaling is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal in het Italiaans zou veranderen. In uw code kunt [`SpeechTranslationConfig`][config] u communiceren met `speech_recognition_language` de instantie en de eigenschap toewijzen.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

De [`speech_recognition_language`][recognitionlang] accommodatie verwacht een tekenreeks voor taal-landformaat. U elke waarde in de kolom **Locale** vermelden in de lijst met ondersteunde [landlocaties/talen.](../../../language-support.md)

## <a name="add-translation-language"></a>Vertaaltaal toevoegen

Een andere veelvoorkomende taak voor spraakvertaling is het opgeven van doelvertaaltalen, er is er ten minste één vereist, maar veelvouden worden ondersteund. In het volgende codefragment worden zowel Frans als Duits als vertaaltaal als doel.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Bij elke [`add_target_language`][addlang]oproep naar , wordt een nieuwe doelvertaaltaal opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de brontaal, is elke doelvertaling beschikbaar als onderdeel van de resulterende vertaalbewerking.

## <a name="initialize-a-translation-recognizer"></a>Een vertaalherkenninginitialiseren

Nadat u een [`SpeechTranslationConfig`][config], de volgende stap is [`TranslationRecognizer`][recognizer]het initialiseren van een . Wanneer u een [`TranslationRecognizer`][recognizer]initialiseren, moet je het `translation_config`doorgeven van uw . Het configuratieobject biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`TranslationRecognizer`][recognizer] er als volgt uit:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`][audioconfig] u een `audio_config` parameter maken [`TranslationRecognizer`][recognizer]en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u als `AudioConfig` volgt naar het object:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audioConfig`te gebruiken, moet u nog steeds een . Wanneer u echter [`AudioConfig`][audioconfig]een - in `use_default_microphone=True`plaats van te `filename="path-to-file.wav"` bellen `filename` met - maakt, belt u met en verstrekt u de parameter.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Spraak vertalen

Om spraak te vertalen, vertrouwt de Speech SDK op een microfoon of een audiobestandsinvoer. Spraakherkenning vindt plaats vóór spraakvertaling. Nadat alle objecten zijn geïnitialiseerd, roept u de functie herkennen-eens aan en krijgt u het resultaat.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Zie [de basisprincipes van spraakherkenning](../../../speech-to-text-basics.md)voor meer informatie over spraak-naar-tekst.

## <a name="synthesize-translations"></a>Vertalingen synthetiseren

Na een succesvolle spraakherkenning en vertaling bevat het resultaat alle vertalingen in een woordenboek. De [`translations`][translations] woordenboeksleutel is de doelvertaaltaal en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens gesynthetiseerd in een andere taal (spraak-naar-spraak).

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object legt `Synthesizing` een gebeurtenis bloot. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme om de gesynthetiseerde audio uit het resultaat van de vertaalherkenning op te halen. Als u naar meerdere talen vertaalt, raadpleegt u [handmatige synthese](#manual-synthesis). Geef de synthesestem op [`voice_name`][voicename] door een gebeurtenishandler `Synthesizing` toe te wijs en een gebeurtenishandler voor de gebeurtenis op te nemen, de audio op te nemen. In het volgende voorbeeld wordt de vertaalde audio opgeslagen als een *.wav-bestand.*

> [!IMPORTANT]
> De gebeurtenisgebaseerde synthese werkt slechts met één vertaling, voeg **geen** meerdere doelvertaaltalen toe. Bovendien moet [`voice_name`][voicename] het dezelfde taal zijn als bijvoorbeeld de doelvertaaltaal; `"de"` kon in `"de-DE-Hedda"`kaart brengen naar .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Handmatige synthese

Het [`translations`][translations] woordenboek kan worden gebruikt om audio uit de vertaaltekst te synthetiseren. Door elke vertaling te herhalen en de vertaling te synthetiseren. Bij het `SpeechSynthesizer` maken `SpeechConfig` van een instantie [`speech_synthesis_voice_name`][speechsynthesisvoicename] moet het object zijn eigenschap hebben ingesteld op de gewenste stem. Het volgende voorbeeld vertaalt naar vijf talen en elke vertaling wordt vervolgens gesynthetiseerd naar een audiobestand in de overeenkomstige neurale taal.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Voor meer informatie over spraaksynthese, zie [de basisprincipes van spraaksynthese.](../../../text-to-speech-basics.md)

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
