---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 09f08e314a634de13a683440ad9fead97ad8a260
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399614"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies onder het gedeelte <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> van het Speech SDK-artikel.

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit `#include` artikel `using` wilt uitvoeren, neemt u de volgende en instructies boven aan het C++-codebestand op.

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

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevingsvariabelen

De voorbeeldbroncode in dit artikel is afhankelijk van omgevingsvariabelen voor het opslaan van gevoelige gegevens, zoals de abonnementssleutel voor spraakbronnen en regio. Het C++-codebestand bevat twee tekenreekswaarden die zijn toegewezen `SPEECH__SUBSCRIPTION__KEY` aan `SPEECH__SERVICE__REGION`de omgevingsvariabelen van de hostmachines, namelijk en . Beide velden zijn op de klasse scope, waardoor ze toegankelijk binnen methode organen van de klasse. Zie [omgevingsvariabelen en toepassingsconfiguratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevingsvariabelen.

```cpp
auto SPEECH__SUBSCRIPTION__KEY = getenv("SPEECH__SUBSCRIPTION__KEY");
auto SPEECH__SERVICE__REGION = getenv("SPEECH__SERVICE__REGION");
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

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakvertaling is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal in het Italiaans zou veranderen. In uw code kunt [`SpeechTranslationConfig`][config] u communiceren `SetSpeechRecognitionLanguage` met de instantie en de methode aanroepen.

```cpp
void translateSpeech() {
    auto translationConfig =
        SpeechTranslationConfig::FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig->SetSpeechRecognitionLanguage("it-IT");
}
```

De [`SpeechRecognitionLanguage`][recognitionlang] accommodatie verwacht een tekenreeks voor taal-landformaat. U elke waarde in de kolom **Locale** vermelden in de lijst met ondersteunde [landlocaties/talen.](../../../language-support.md)

## <a name="add-translation-language"></a>Vertaaltaal toevoegen

Een andere veelvoorkomende taak voor spraakvertaling is het opgeven van doelvertaaltalen, er is er ten minste één vereist, maar veelvouden worden ondersteund. In het volgende codefragment worden zowel Frans als Duits als vertaaltaal als doel.

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

Bij elke [`AddTargetLanguage`][addlang]oproep naar , wordt een nieuwe doelvertaaltaal opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de brontaal, is elke doelvertaling beschikbaar als onderdeel van de resulterende vertaalbewerking.

## <a name="initialize-a-translation-recognizer"></a>Een vertaalherkenninginitialiseren

Nadat u een [`SpeechTranslationConfig`][config], de volgende stap is [`TranslationRecognizer`][recognizer]het initialiseren van een . Wanneer u een [`TranslationRecognizer`][recognizer]initialiseren, moet je het `translationConfig`doorgeven van uw . Het configuratieobject biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`TranslationRecognizer`][recognizer] er als volgt uit:

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

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`][audioconfig] u een `audioConfig` parameter maken [`TranslationRecognizer`][recognizer]en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u als `AudioConfig` volgt naar het object:

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

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audioConfig`te gebruiken, moet u nog steeds een . Wanneer u echter [`AudioConfig`][audioconfig]een - `FromDefaultMicrophoneInput`in plaats van `FromWavFileInput` aanteroepen - maakt, belt u en geeft u de `filename` parameter door.

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

Om spraak te vertalen, vertrouwt de Speech SDK op een microfoon of een audiobestandsinvoer. Spraakherkenning vindt plaats vóór spraakvertaling. Nadat alle objecten zijn geïnitialiseerd, roept u de functie herkennen-eens aan en krijgt u het resultaat.

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

Zie [de basisprincipes van spraakherkenning](../../../speech-to-text-basics.md)voor meer informatie over spraak-naar-tekst.

## <a name="synthesize-translations"></a>Vertalingen synthetiseren

Na een succesvolle spraakherkenning en vertaling bevat het resultaat alle vertalingen in een woordenboek. De [`Translations`][translations] woordenboeksleutel is de doelvertaaltaal en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens gesynthetiseerd in een andere taal (spraak-naar-spraak).

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object legt `Synthesizing` een gebeurtenis bloot. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme om de gesynthetiseerde audio uit het resultaat van de vertaalherkenning op te halen. Als u naar meerdere talen vertaalt, raadpleegt u [handmatige synthese](#manual-synthesis). Geef de synthesestem op [`SetVoiceName`][voicename] door een gebeurtenishandler `Synthesizing` toe te wijs en een gebeurtenishandler voor de gebeurtenis op te nemen, de audio op te nemen. In het volgende voorbeeld wordt de vertaalde audio opgeslagen als een *.wav-bestand.*

> [!IMPORTANT]
> De gebeurtenisgebaseerde synthese werkt slechts met één vertaling, voeg **geen** meerdere doelvertaaltalen toe. Bovendien moet [`SetVoiceName`][voicename] het dezelfde taal zijn als bijvoorbeeld de doelvertaaltaal; `"de"` kon in `"de-DE-Hedda"`kaart brengen naar .

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

### <a name="manual-synthesis"></a>Handmatige synthese

Het [`Translations`][translations] woordenboek kan worden gebruikt om audio uit de vertaaltekst te synthetiseren. Door elke vertaling te herhalen en de vertaling te synthetiseren. Bij het `SpeechSynthesizer` maken `SpeechConfig` van een instantie [`SetSpeechSynthesisVoiceName`][speechsynthesisvoicename] moet het object zijn eigenschap hebben ingesteld op de gewenste stem. Het volgende voorbeeld vertaalt naar vijf talen en elke vertaling wordt vervolgens gesynthetiseerd naar een audiobestand in de overeenkomstige neurale taal.

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

Voor meer informatie over spraaksynthese, zie [de basisprincipes van spraaksynthese.](../../../text-to-speech-basics.md)

[config]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig
[audioconfig]: https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig
[recognizer]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognizer
[recognitionlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechrecognitionlanguage
[addlang]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#addtargetlanguage
[translations]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-translationrecognitionresult#translations
[voicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/translation-speechtranslationconfig#setvoicename
[speechsynthesisvoicename]: https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setspeechsynthesisvoicename
