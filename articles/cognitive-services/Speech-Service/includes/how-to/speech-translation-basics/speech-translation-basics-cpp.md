---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 09f08e314a634de13a683440ad9fead97ad8a260
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399614"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies in de sectie <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> SDK voor spraak ophalen</a> van het artikel Speech SDK.

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voor beelden in dit artikel wilt uitvoeren, `#include` voegt `using` u de volgende en-instructies toe boven aan het C++-code bestand.

```cpp
#include <iostream> // cin, cout
#include <fstream>
#include <string>
#include <stdio.h>
#include <stdlib.h>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
using namespace Microsoft::CognitiveServices::Speech::Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevings variabelen

De voorbeeld bron code in dit artikel is afhankelijk van omgevings variabelen voor het opslaan van gevoelige gegevens, zoals de sleutel van het abonnement voor spraak bronnen en de regio. Het C++-code bestand bevat twee teken reeks waarden die zijn toegewezen uit de omgevings variabelen van `SPEECH__SUBSCRIPTION__KEY` de `SPEECH__SERVICE__REGION`hostcomputer, te weten en. Beide velden bevinden zich in het bereik klasse, waardoor ze toegankelijk zijn in methode-instanties van de-klasse. Zie [omgevings variabelen en toepassings configuratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevings variabelen.

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");

void translateSpeech() {
    auto config =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
}

int main(int argc, char** argv) {
    setlocale(LC_ALL, "");
    translateSpeech();
    return 0;
}
```

## <a name="change-source-language"></a>Bron taal wijzigen

Een veelvoorkomende taak van spraak omzetting is het opgeven van de invoer-(of bron-) taal. Laten we eens kijken hoe u de invoer taal wijzigt in Italiaans. In uw code communiceert u met [`SpeechTranslationConfig`][config] het exemplaar en roept `SetSpeechRecognitionLanguage` u de-methode aan.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

De [`SpeechRecognitionLanguage`][recognitionlang] eigenschap verwacht een indelings teken reeks voor taal-land instellingen. U kunt elke waarde in de kolom **land instelling** opgeven in de lijst met ondersteunde [land instellingen/talen](../../../language-support.md).

## <a name="add-translation-language"></a>Vertaal taal toevoegen

Een andere veelvoorkomende taak van spraak omzetting is het opgeven van talen voor doel omzetting, ten minste één is vereist, maar meerdere worden ondersteund. In het volgende code fragment, zowel Frans als Duits als Vertaal taal doelen.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig->SetSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig->AddTargetLanguage("fr");
    translationConfig->AddTargetLanguage("de");
}
```

Bij elke aanroep van [`AddTargetLanguage`][addlang]wordt een nieuwe doel taal voor vertalen opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de bron taal, is elke doel omzetting beschikbaar als onderdeel van de resulterende Vertaal bewerking.

## <a name="initialize-a-translation-recognizer"></a>Een omzettings herkenning initialiseren

Nadat u een [`SpeechTranslationConfig`][config]hebt gemaakt, is de volgende stap het initialiseren van [`TranslationRecognizer`][recognizer]een. Wanneer u een [`TranslationRecognizer`][recognizer]initialiseert, moet u het door geven aan uw `translationConfig`. Het configuratie object bevat de referenties die de speech-service nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaard microfoon van uw apparaat, ziet u het [`TranslationRecognizer`][recognizer] volgende:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
}
```

Als u het audio-invoer apparaat wilt opgeven, moet u een [`AudioConfig`][audioconfig] maken en de `audioConfig` para meter opgeven bij het initialiseren van uw [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het apparaat voor audio-invoer](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u als volgt naar `AudioConfig` het object:

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

Als u een audio bestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig`opgeven. Wanneer [`AudioConfig`][audioconfig]u echter een maakt in plaats van het aanroepen `FromDefaultMicrophoneInput`van, roept `FromWavFileInput` u de para `filename` meter op en geeft u deze door.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto audioConfig = AudioConfig::FromWavFileInput("YourAudioFile.wav");
    auto recognizer = TranslationRecognizer::FromConfig(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Spraak vertalen

Om spraak te kunnen vertalen, is de spraak-SDK afhankelijk van een microfoon of een audio bestand invoer. Spraak herkenning vindt plaats voordat spraak wordt vertaald. Wanneer alle objecten zijn geïnitialiseerd, roept u de functie recognize Once aan en haalt u het resultaat op.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    string fromLanguage = "en-US";
    string toLanguages[3] = { "it", "fr", "de" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

Zie [basis beginselen van spraak herkenning](../../../speech-to-text-basics.md)voor meer informatie over spraak naar tekst.

## <a name="synthesize-translations"></a>Vertalingen voor synthesizer

Na een geslaagde spraak herkenning en-omzetting bevat het resultaat alle vertalingen in een woorden lijst. De [`Translations`][translations] woordenlijst sleutel is de taal voor de doel omzetting en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens in een andere taal (spraak naar spraak) worden gesynthesizerd.

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object toont een `Synthesizing` gebeurtenis. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme voor het ophalen van de gesynthesizerde audio uit het resultaat van de vertalings herkenning. Zie [hand matige synthese](#manual-synthesis)als u naar meerdere talen wilt vertalen. Geef de synthese stem op door een [`SetVoiceName`][voicename] gebeurtenis-handler `Synthesizing` toe te wijzen en de audio op te halen. In het volgende voor beeld wordt de vertaalde audio opgeslagen als *WAV* -bestand.

> [!IMPORTANT]
> De op gebeurtenissen gebaseerde synthese werkt alleen met één vertaling, Voeg **geen** meerdere talen voor doel omzetting toe. Daarnaast moet het [`SetVoiceName`][voicename] dezelfde taal zijn als de taal van de doel omzetting, bijvoorbeeld; `"de"` kan worden toegewezen `"de-DE-Hedda"`aan.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguage = "de";
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    translationConfig->AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig->SetVoiceName("de-DE-Hedda");

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);
    recognizer->Synthesizing.Connect([](const TranslationSynthesisEventArgs& e)
        {
            auto audio = e.Result->Audio;
            auto size = audio.size();
            cout << "Audio synthesized: " << size << " byte(s)" << (size == 0 ? "(COMPLETE)" : "") << std::endl;

            if (size > 0) {
                ofstream file("translation.wav", ios::out | ios::binary);
                auto audioData = audio.data();
                file.write((const char*)audioData, sizeof(audio[0]) * size);
                file.close();
            }
        });

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;
        }
    }
}
```

### <a name="manual-synthesis"></a>Hand matige synthese

De [`Translations`][translations] woorden lijst kan worden gebruikt om de audio van de Vertaal tekst te defragmenteren. Herhaal elke vertaling en synthesizer de vertaling. Bij het maken `SpeechSynthesizer` van een exemplaar `SpeechConfig` moet de [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] eigenschap van het object op de gewenste stem zijn ingesteld. In het volgende voor beeld worden de vijf talen omgezet en elke vertaling wordt vervolgens op een audio bestand in de bijbehorende Neural-taal gesynthesizerd.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    auto fromLanguage = "en-US";
    auto toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig->SetSpeechRecognitionLanguage(fromLanguage);
    for (auto language : toLanguages) {
        translationConfig->AddTargetLanguage(language);
    }

    auto recognizer = TranslationRecognizer::FromConfig(translationConfig);

    cout << "Say something in '" << fromLanguage << "' and we'll translate...\n";

    auto result = recognizer->RecognizeOnceAsync().get();
    if (result->Reason == ResultReason::TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        map<string, string> languageToVoiceMap;
        languageToVoiceMap["de"] = "de-DE-KatjaNeural";
        languageToVoiceMap["en"] = "en-US-AriaNeural";
        languageToVoiceMap["it"] = "it-IT-ElsaNeural";
        languageToVoiceMap["pt"] = "pt-BR-FranciscaNeural";
        languageToVoiceMap["zh-Hans"] = "zh-CN-XiaoxiaoNeural";

        cout << "Recognized: \"" << result->Text << "\"" << std::endl;
        for (auto pair : result->Translations)
        {
            auto language = pair.first;
            auto translation = pair.second;
            cout << "Translated into '" << language << "': " << translation << std::endl;

            auto speech_config =
                SpeechConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
            speech_config->SetSpeechSynthesisVoiceName(languageToVoiceMap[language]);

            auto audio_config = AudioConfig::FromWavFileOutput(language + "-translation.wav");
            auto synthesizer = SpeechSynthesizer::FromConfig(speech_config, audio_config);

            synthesizer->SpeakTextAsync(translation).get();
        }
    }
}
```

Zie [basis beginselen van spraak synthese](../../../text-to-speech-basics.md)voor meer informatie over spraak synthese.

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
