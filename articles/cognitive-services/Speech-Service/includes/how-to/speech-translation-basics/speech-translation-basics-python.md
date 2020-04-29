---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 17d8c0157fcd478d01452167d240fb67daeeda5b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399609"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies in de sectie <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> SDK voor spraak ophalen</a> van het artikel Speech SDK.

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voor beelden in dit artikel wilt uitvoeren, `import` voegt u de volgende instructies toe boven aan het python-code bestand.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevings variabelen

De voorbeeld bron code in dit artikel is afhankelijk van omgevings variabelen voor het opslaan van gevoelige gegevens, zoals de sleutel van het abonnement voor spraak bronnen en de regio. Het python-code bestand bevat twee waarden die zijn toegewezen uit de omgevings variabelen van de `SPEECH__SUBSCRIPTION__KEY` hostcomputer `SPEECH__SERVICE__REGION`, te weten en. Beide variabelen bevinden zich in het globale bereik, waardoor ze toegankelijk zijn in de functie definitie van het code bestand. Zie [omgevings variabelen en toepassings configuratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevings variabelen.

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Een configuratie voor spraak omzetting maken

Als u de spraak service wilt aanroepen met behulp van de Speech SDK [`SpeechTranslationConfig`][config], moet u een maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eind punt, de host of het autorisatie token.

> [!TIP]
> Ongeacht of u spraak herkenning, spraak synthese, vertaling of intentie herkenning uitvoert, maakt u altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechTranslationConfig`][config]kunt initialiseren:

* Met een abonnement: Geef een sleutel en de bijbehorende regio door.
* Met een eind punt: Pass in een speech service-eind punt. Een sleutel-of autorisatie token is optioneel.
* Met een host: Geef een hostadres door. Een sleutel-of autorisatie token is optioneel.
* Met een autorisatie token: Geef een autorisatie token en de bijbehorende regio door.

Laten we eens kijken hoe een [`SpeechTranslationConfig`][config] is gemaakt met behulp van een sleutel en regio. Bekijk de [ondersteunings](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pagina voor regio's om uw regio-id te vinden.

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Bron taal wijzigen

Een veelvoorkomende taak van spraak omzetting is het opgeven van de invoer-(of bron-) taal. Laten we eens kijken hoe u de invoer taal wijzigt in Italiaans. In uw code, communiceert u [`SpeechTranslationConfig`][config] met het exemplaar en wijst `speech_recognition_language` u deze toe aan de eigenschap.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

De [`speech_recognition_language`][recognitionlang] eigenschap verwacht een indelings teken reeks voor taal-land instellingen. U kunt elke waarde in de kolom **land instelling** opgeven in de lijst met ondersteunde [land instellingen/talen](../../../language-support.md).

## <a name="add-translation-language"></a>Vertaal taal toevoegen

Een andere veelvoorkomende taak van spraak omzetting is het opgeven van talen voor doel omzetting, ten minste één is vereist, maar meerdere worden ondersteund. In het volgende code fragment, zowel Frans als Duits als Vertaal taal doelen.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Bij elke aanroep van [`add_target_language`][addlang]wordt een nieuwe doel taal voor vertalen opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de bron taal, is elke doel omzetting beschikbaar als onderdeel van de resulterende Vertaal bewerking.

## <a name="initialize-a-translation-recognizer"></a>Een omzettings herkenning initialiseren

Nadat u een [`SpeechTranslationConfig`][config]hebt gemaakt, is de volgende stap het initialiseren van [`TranslationRecognizer`][recognizer]een. Wanneer u een [`TranslationRecognizer`][recognizer]initialiseert, moet u het door geven aan uw `translation_config`. Het configuratie object bevat de referenties die de speech-service nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaard microfoon van uw apparaat, ziet u het [`TranslationRecognizer`][recognizer] volgende:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Als u het audio-invoer apparaat wilt opgeven, moet u een [`AudioConfig`][audioconfig] maken en de `audio_config` para meter opgeven bij het initialiseren van uw [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het apparaat voor audio-invoer](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u als volgt naar `AudioConfig` het object:

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

Als u een audio bestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig`opgeven. Wanneer u [`AudioConfig`][audioconfig]echter een maakt in plaats van aan `use_default_microphone=True`te roepen, roept u aan `filename="path-to-file.wav"` en geeft u de `filename` para meter op.

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

Om spraak te kunnen vertalen, is de spraak-SDK afhankelijk van een microfoon of een audio bestand invoer. Spraak herkenning vindt plaats voordat spraak wordt vertaald. Wanneer alle objecten zijn geïnitialiseerd, roept u de functie recognize Once aan en haalt u het resultaat op.

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

Zie [basis beginselen van spraak herkenning](../../../speech-to-text-basics.md)voor meer informatie over spraak naar tekst.

## <a name="synthesize-translations"></a>Vertalingen voor synthesizer

Na een geslaagde spraak herkenning en-omzetting bevat het resultaat alle vertalingen in een woorden lijst. De [`translations`][translations] woordenlijst sleutel is de taal voor de doel omzetting en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens in een andere taal (spraak naar spraak) worden gesynthesizerd.

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object toont een `Synthesizing` gebeurtenis. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme voor het ophalen van de gesynthesizerde audio uit het resultaat van de vertalings herkenning. Zie [hand matige synthese](#manual-synthesis)als u naar meerdere talen wilt vertalen. Geef de synthese stem op door een [`voice_name`][voicename] gebeurtenis-handler `Synthesizing` toe te wijzen en de audio op te halen. In het volgende voor beeld wordt de vertaalde audio opgeslagen als *WAV* -bestand.

> [!IMPORTANT]
> De op gebeurtenissen gebaseerde synthese werkt alleen met één vertaling, Voeg **geen** meerdere talen voor doel omzetting toe. Daarnaast moet het [`voice_name`][voicename] dezelfde taal zijn als de taal van de doel omzetting, bijvoorbeeld; `"de"` kan worden toegewezen `"de-DE-Hedda"`aan.

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

### <a name="manual-synthesis"></a>Hand matige synthese

De [`translations`][translations] woorden lijst kan worden gebruikt om de audio van de Vertaal tekst te defragmenteren. Herhaal elke vertaling en synthesizer de vertaling. Bij het maken `SpeechSynthesizer` van een exemplaar `SpeechConfig` moet de [`speech_synthesis_voice_name`][speechsynthesisvoicename] eigenschap van het object op de gewenste stem zijn ingesteld. In het volgende voor beeld worden de vijf talen omgezet en elke vertaling wordt vervolgens op een audio bestand in de bijbehorende Neural-taal gesynthesizerd.

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

Zie [basis beginselen van spraak synthese](../../../text-to-speech-basics.md)voor meer informatie over spraak synthese.

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
