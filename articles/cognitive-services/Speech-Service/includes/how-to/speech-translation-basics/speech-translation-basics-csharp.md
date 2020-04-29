---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bac2ed447c9055f095e604725591c487378f5091
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399670"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Volg afhankelijk van uw platform de instructies in de sectie <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> SDK voor spraak ophalen</a> van het artikel Speech SDK.

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voor beelden in dit artikel wilt uitvoeren, `using` voegt u de volgende instructies toe boven aan het *Program.cs* -bestand.

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

## <a name="sensitive-data-and-environment-variables"></a>Gevoelige gegevens en omgevings variabelen

De voorbeeld bron code in dit artikel is afhankelijk van omgevings variabelen voor het opslaan van gevoelige gegevens, zoals de sleutel van het abonnement voor spraak bronnen en de regio. De `Program` klasse bevat twee `static readonly string` waarden die zijn toegewezen uit de omgevings variabelen van de hostcomputer `SPEECH__SUBSCRIPTION__KEY` , `SPEECH__SERVICE__REGION`te weten en. Beide velden bevinden zich in het bereik klasse, waardoor ze toegankelijk zijn in methode-instanties van de-klasse. Zie [omgevings variabelen en toepassings configuratie](../../../../cognitive-services-security.md#environment-variables-and-application-configuration)voor meer informatie over omgevings variabelen.

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

## <a name="change-source-language"></a>Bron taal wijzigen

Een veelvoorkomende taak van spraak omzetting is het opgeven van de invoer-(of bron-) taal. Laten we eens kijken hoe u de invoer taal wijzigt in Italiaans. In uw code, communiceert u [`SpeechTranslationConfig`][config] met het exemplaar en wijst `SpeechRecognitionLanguage` u deze toe aan de eigenschap.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

De [`SpeechRecognitionLanguage`][recognitionlang] eigenschap verwacht een indelings teken reeks voor taal-land instellingen. U kunt elke waarde in de kolom **land instelling** opgeven in de lijst met ondersteunde [land instellingen/talen](../../../language-support.md).

## <a name="add-translation-language"></a>Vertaal taal toevoegen

Een andere veelvoorkomende taak van spraak omzetting is het opgeven van talen voor doel omzetting, ten minste één is vereist, maar meerdere worden ondersteund. In het volgende code fragment, zowel Frans als Duits als Vertaal taal doelen.

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

Bij elke aanroep van [`AddTargetLanguage`][addlang]wordt een nieuwe doel taal voor vertalen opgegeven. Met andere woorden, wanneer spraak wordt herkend vanuit de bron taal, is elke doel omzetting beschikbaar als onderdeel van de resulterende Vertaal bewerking.

## <a name="initialize-a-translation-recognizer"></a>Een omzettings herkenning initialiseren

Nadat u een [`SpeechTranslationConfig`][config]hebt gemaakt, is de volgende stap het initialiseren van [`TranslationRecognizer`][recognizer]een. Wanneer u een [`TranslationRecognizer`][recognizer]initialiseert, moet u het door geven aan uw `translationConfig`. Het configuratie object bevat de referenties die de speech-service nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaard microfoon van uw apparaat, ziet u het [`TranslationRecognizer`][recognizer] volgende:

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

Als u het audio-invoer apparaat wilt opgeven, moet u een [`AudioConfig`][audioconfig] maken en de `audioConfig` para meter opgeven bij het initialiseren van uw [`TranslationRecognizer`][recognizer].

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het apparaat voor audio-invoer](../../../how-to-select-audio-input-devices.md).

Eerst verwijst u als volgt naar `AudioConfig` het object:

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

Als u een audio bestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig`opgeven. Wanneer [`AudioConfig`][audioconfig]u echter een maakt in plaats van het aanroepen `FromDefaultMicrophoneInput`van, roept `FromWavFileInput` u de para `filename` meter op en geeft u deze door.

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

Om spraak te kunnen vertalen, is de spraak-SDK afhankelijk van een microfoon of een audio bestand invoer. Spraak herkenning vindt plaats voordat spraak wordt vertaald. Wanneer alle objecten zijn geïnitialiseerd, roept u de functie recognize Once aan en haalt u het resultaat op.

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

Zie [basis beginselen van spraak herkenning](../../../speech-to-text-basics.md)voor meer informatie over spraak naar tekst.

## <a name="synthesize-translations"></a>Vertalingen voor synthesizer

Na een geslaagde spraak herkenning en-omzetting bevat het resultaat alle vertalingen in een woorden lijst. De [`Translations`][translations] woordenlijst sleutel is de taal voor de doel omzetting en de waarde is de vertaalde tekst. Herkende spraak kan worden vertaald en vervolgens in een andere taal (spraak naar spraak) worden gesynthesizerd.

### <a name="event-based-synthesis"></a>Op gebeurtenissen gebaseerde synthese

Het `TranslationRecognizer` object toont een `Synthesizing` gebeurtenis. De gebeurtenis wordt meerdere keren geactiveerd en biedt een mechanisme voor het ophalen van de gesynthesizerde audio uit het resultaat van de vertalings herkenning. Zie [hand matige synthese](#manual-synthesis)als u naar meerdere talen wilt vertalen. Geef de synthese stem op door een [`VoiceName`][voicename] gebeurtenis-handler `Synthesizing` toe te wijzen en de audio op te halen. In het volgende voor beeld wordt de vertaalde audio opgeslagen als *WAV* -bestand.

> [!IMPORTANT]
> De op gebeurtenissen gebaseerde synthese werkt alleen met één vertaling, Voeg **geen** meerdere talen voor doel omzetting toe. Daarnaast moet het [`VoiceName`][voicename] dezelfde taal zijn als de taal van de doel omzetting, bijvoorbeeld; `"de"` kan worden toegewezen `"de-DE-Hedda"`aan.

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

### <a name="manual-synthesis"></a>Hand matige synthese

De [`Translations`][translations] woorden lijst kan worden gebruikt om de audio van de Vertaal tekst te defragmenteren. Herhaal elke vertaling en synthesizer de vertaling. Bij het maken `SpeechSynthesizer` van een exemplaar `SpeechConfig` moet de [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] eigenschap van het object op de gewenste stem zijn ingesteld. In het volgende voor beeld worden de vijf talen omgezet en elke vertaling wordt vervolgens op een audio bestand in de bijbehorende Neural-taal gesynthesizerd.

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

Zie [basis beginselen van spraak synthese](../../../text-to-speech-basics.md)voor meer informatie over spraak synthese.

[config]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig?view=azure-dotnet
[audioconfig]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet
[recognizer]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer?view=azure-dotnet
[recognitionlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet
[addlang]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage?view=azure-dotnet
[translations]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations?view=azure-dotnet
[voicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename?view=azure-dotnet
[speechsynthesisvoicename]: https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename?view=azure-dotnet
