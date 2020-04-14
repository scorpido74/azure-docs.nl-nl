---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 0f5570f1c80fdecb4db6c8baf22ad05f1e4a32a6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266656"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies onder het gedeelte <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Get the Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> van het Speech SDK-artikel.

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit `using` artikel wilt uitvoeren, neemt u de volgende instructies op boven aan het *Program.cs* bestand.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevingsvariabelen

De voorbeeldbroncode in dit artikel is afhankelijk van omgevingsvariabelen voor het opslaan van gevoelige gegevens, zoals de abonnementssleutel voor spraakbronnen en regio. De `Program` klasse `static readonly string` bevat twee waarden die zijn toegewezen aan `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`de omgevingsvariabelen van de hostmachines, namelijk en . Beide velden zijn op de klasse scope, waardoor ze toegankelijk binnen methode organen van de klasse. Zie [omgevingsvariabelen en toepassingsconfiguratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevingsvariabelen.

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
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

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakvertaling is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal in het Italiaans zou veranderen. In uw code kunt [`SpeechTranslationConfig`][config] u communiceren met `SpeechRecognitionLanguage` de instantie en de eigenschap toewijzen.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

De [`SpeechRecognitionLanguage`][recognitionlang] accommodatie verwacht een tekenreeks voor taal-landformaat. U elke waarde in de kolom **Locale** vermelden in de lijst met ondersteunde [landlocaties/talen.](../../../language-support.md)

## <a name="add-translation-language"></a>Vertaaltaal toevoegen

Een andere veelvoorkomende taak voor spraakvertaling is het opgeven van doelvertaaltalen, er is er ten minste één vereist, maar veelvouden worden ondersteund. In het volgende codefragment worden zowel Frans als Duits als vertaaltaal als doel.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Bij elke [`AddTargetLanguage`][addlang]oproep naar , wordt een nieuwe doelvertaaltaal opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de brontaal, is elke doelvertaling beschikbaar als onderdeel van de resulterende vertaalbewerking.

## <a name="initialize-a-translation-recognizer"></a>Een vertaalherkenninginitialiseren

Nadat u een [`SpeechTranslationConfig`][config], de volgende stap is [`TranslationRecognizer`][recognizer]het initialiseren van een . Wanneer u een [`TranslationRecognizer`][recognizer]initialiseren, moet je het `translationConfig`doorgeven van uw . Het configuratieobject biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`TranslationRecognizer`][recognizer] er als volgt uit:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`][audioconfig] u een `audioConfig` parameter maken [`TranslationRecognizer`][recognizer]en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u als `AudioConfig` volgt naar het object:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audioConfig`te gebruiken, moet u nog steeds een . Wanneer u echter [`AudioConfig`][audioconfig]een - `FromDefaultMicrophoneInput`in plaats van `FromWavFileInput` aanteroepen - maakt, belt u en geeft u de `filename` parameter door.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Spraak vertalen

Om spraak te vertalen, vertrouwt de Speech SDK op een microfoon of een audiobestandsinvoer. Spraakherkenning vindt plaats vóór spraakvertaling. Nadat alle objecten zijn geïnitialiseerd, roept u de functie herkennen-eens aan en krijgt u het resultaat.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Zie [de basisprincipes van spraakherkenning](../../../speech-to-text-basics.md)voor meer informatie over spraak-naar-tekst.

## <a name="synthesize-translations"></a>Vertalingen synthetiseren

Na een succesvolle spraakherkenning en vertaling bevat het resultaat alle vertalingen in een woordenboek. De [`Translations`][translations] woordenboeksleutel is de doelvertaaltaal en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens gesynthetiseerd in een andere taal (spraak-naar-spraak).

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object legt `Synthesizing` een gebeurtenis bloot. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme om de gesynthetiseerde audio uit het resultaat van de vertaalherkenning op te halen. Als u naar meerdere talen vertaalt, raadpleegt u [handmatige synthese](#manual-synthesis). Geef de synthesestem op [`VoiceName`][voicename] door een gebeurtenishandler `Synthesizing` toe te wijs en een gebeurtenishandler voor de gebeurtenis op te nemen, de audio op te nemen. In het volgende voorbeeld wordt de vertaalde audio opgeslagen als een *.wav-bestand.*

> [!IMPORTANT]
> De gebeurtenisgebaseerde synthese werkt slechts met één vertaling, voeg **geen** meerdere doelvertaaltalen toe. Bovendien moet [`VoiceName`][voicename] het dezelfde taal zijn als bijvoorbeeld de doelvertaaltaal; `"de"` kon in `"de-DE-Hedda"`kaart brengen naar .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Handmatige synthese

Het [`Translations`][translations] woordenboek kan worden gebruikt om audio uit de vertaaltekst te synthetiseren. Door elke vertaling te herhalen en de vertaling te synthetiseren. Bij het `SpeechSynthesizer` maken `SpeechConfig` van een instantie [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] moet het object zijn eigenschap hebben ingesteld op de gewenste stem. Het volgende voorbeeld vertaalt naar vijf talen en elke vertaling wordt vervolgens gesynthetiseerd naar een audiobestand in de overeenkomstige neurale taal.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Voor meer informatie over spraaksynthese, zie [de basisprincipes van spraaksynthese.](../../../text-to-speech-basics.md)

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
