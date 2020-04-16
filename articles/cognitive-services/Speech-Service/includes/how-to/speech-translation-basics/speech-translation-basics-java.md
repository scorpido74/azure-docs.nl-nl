---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 73e6e117428808aae39e361a3b119e9b2af1ac27
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399591"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies onder het gedeelte <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> van het Speech SDK-artikel.

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit `import` artikel wilt uitvoeren, neemt u de volgende instructies op boven aan de **. *Java-codebestand.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevingsvariabelen

De voorbeeldbroncode in dit artikel is afhankelijk van omgevingsvariabelen voor het opslaan van gevoelige gegevens, zoals de abonnementssleutel voor spraakbronnen en regio. Het Java-codebestand `static final String` bevat twee waarden die zijn toegewezen `SPEECH__SUBSCRIPTION__KEY` aan `SPEECH__SERVICE__REGION`de omgevingsvariabelen van de hostmachines, namelijk en . Beide velden zijn op de klasse scope, waardoor ze toegankelijk binnen methode organen van de klasse. Zie [omgevingsvariabelen en toepassingsconfiguratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevingsvariabelen.

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
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

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakvertaling is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal in het Italiaans zou veranderen. In uw code kunt [`SpeechTranslationConfig`][config] u communiceren `setSpeechRecognitionLanguage` met de instantie en de methode aanroepen.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

De [`setSpeechRecognitionLanguage`][recognitionlang] functie verwacht een tekenreeks voor taal-landnotatie. U elke waarde in de kolom **Locale** vermelden in de lijst met ondersteunde [landlocaties/talen.](../../../language-support.md)

## <a name="add-translation-language"></a>Vertaaltaal toevoegen

Een andere veelvoorkomende taak voor spraakvertaling is het opgeven van doelvertaaltalen, er is er ten minste één vereist, maar veelvouden worden ondersteund. In het volgende codefragment worden zowel Frans als Duits als vertaaltaal als doel.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Bij elke [`addTargetLanguage`][addlang]oproep naar , wordt een nieuwe doelvertaaltaal opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de brontaal, is elke doelvertaling beschikbaar als onderdeel van de resulterende vertaalbewerking.

## <a name="initialize-a-translation-recognizer"></a>Een vertaalherkenninginitialiseren

Nadat u een [`SpeechTranslationConfig`][config], de volgende stap is [`TranslationRecognizer`][recognizer]het initialiseren van een . Wanneer u een [`TranslationRecognizer`][recognizer]initialiseren, moet je het `translationConfig`doorgeven van uw . Het configuratieobject biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`TranslationRecognizer`][recognizer] er als volgt uit:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`][audioconfig] u een `audioConfig` parameter maken [`TranslationRecognizer`][recognizer]en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u als `AudioConfig` volgt naar het object:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audioConfig`te gebruiken, moet u nog steeds een . Wanneer u echter [`AudioConfig`][audioconfig]een - `fromDefaultMicrophoneInput`in plaats van `fromWavFileInput` aanteroepen - maakt, belt u en geeft u de `filename` parameter door.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Spraak vertalen

Om spraak te vertalen, vertrouwt de Speech SDK op een microfoon of een audiobestandsinvoer. Spraakherkenning vindt plaats vóór spraakvertaling. Nadat alle objecten zijn geïnitialiseerd, roept u de functie herkennen-eens aan en krijgt u het resultaat.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Zie [de basisprincipes van spraakherkenning](../../../speech-to-text-basics.md)voor meer informatie over spraak-naar-tekst.

## <a name="synthesize-translations"></a>Vertalingen synthetiseren

Na een succesvolle spraakherkenning en vertaling bevat het resultaat alle vertalingen in een woordenboek. De [`getTranslations`][translations] functie retourneert een woordenboek met de sleutel als doelvertaaltaal en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens gesynthetiseerd in een andere taal (spraak-naar-spraak).

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object legt `synthesizing` een gebeurtenis bloot. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme om de gesynthetiseerde audio uit het resultaat van de vertaalherkenning op te halen. Als u naar meerdere talen vertaalt, raadpleegt u [handmatige synthese](#manual-synthesis). Geef de synthesestem op [`setVoiceName`][voicename] door een gebeurtenishandler `synthesizing` toe te wijs en een gebeurtenishandler voor de gebeurtenis op te nemen, de audio op te nemen. In het volgende voorbeeld wordt de vertaalde audio opgeslagen als een *.wav-bestand.*

> [!IMPORTANT]
> De gebeurtenisgebaseerde synthese werkt slechts met één vertaling, voeg **geen** meerdere doelvertaaltalen toe. Bovendien moet [`setVoiceName`][voicename] het dezelfde taal zijn als bijvoorbeeld de doelvertaaltaal; `"de"` kon in `"de-DE-Hedda"`kaart brengen naar .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Handmatige synthese

De [`getTranslations`][translations] functie retourneert een woordenboek dat kan worden gebruikt om audio uit de vertaaltekst te synthetiseren. Door elke vertaling te herhalen en de vertaling te synthetiseren. Bij het `SpeechSynthesizer` maken `SpeechConfig` van een instantie [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] moet het object zijn eigenschap hebben ingesteld op de gewenste stem. Het volgende voorbeeld vertaalt naar vijf talen en elke vertaling wordt vervolgens gesynthetiseerd naar een audiobestand in de overeenkomstige neurale taal.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Voor meer informatie over spraaksynthese, zie [de basisprincipes van spraaksynthese.](../../../text-to-speech-basics.md)

[config]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig?view=azure-java-stable
[audioconfig]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig?view=azure-java-stable
[recognizer]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer?view=azure-java-stable
[recognitionlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable
[addlang]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage?view=azure-java-stable
[translations]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations?view=azure-java-stable
[voicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename?view=azure-java-stable
[speechsynthesisvoicename]: https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename?view=azure-java-stable
