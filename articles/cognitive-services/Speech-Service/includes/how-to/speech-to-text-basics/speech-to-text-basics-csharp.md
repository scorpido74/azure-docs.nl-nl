---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 982c3c6011936c184c55dd92a76d4aec023baaf6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399734"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren. Gebruik afhankelijk van uw platform de volgende instructies:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET-kern<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Eenheid<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin Xamarin<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de spraakservice wilt aanroepen met [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) hoe een wordt gemaakt met behulp van een sleutel en regio. Zie de [pagina regioondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden.

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Initialiseren van een herkenningspunt

Nadat u een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet), de volgende stap is [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)het initialiseren van een . Wanneer u een [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)initialiseren, moet je het `speechConfig`doorgeven van uw . Dit biedt de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak herkent met de standaardmicrofoon van uw apparaat, ziet deze [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) er als volgt uit:

```csharp
using var recognizer = new SpeechRecognizer(speechConfig);
```

Als u het audio-invoerapparaat wilt opgeven, moet [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) u een `audioConfig` parameter maken [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)en de parameter opgeven bij het initialiseren van uw .

> [!TIP]
> [Meer informatie over het krijgen van de apparaat-id voor uw audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Voeg eerst de `using` volgende instructie toe.

```csharp
using Microsoft.CognitiveServices.Speech.Audio;
```

Vervolgens u als volgt `AudioConfig` naar het object verwijzen:

```csharp
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Als u een audiobestand wilt verstrekken in plaats van een microfoon `audioConfig`te gebruiken, moet u nog steeds een . Wanneer u echter [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet)een - `FromDefaultMicrophoneInput`in plaats van `FromWavFileOutput` aanteroepen - maakt, belt u en geeft u de `filename` parameter door.


```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Spraak herkennen

De [klasse Recognizeer](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) voor de SpraakSDK voor C# legt een aantal methoden bloot die u gebruiken voor spraakherkenning.

* Single-shot herkenning (async) - Voert herkenning uit in een niet-blokkerende (asynchrone) modus. Dit herkent één utterance. Het einde van een enkele utterance wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio wordt verwerkt.
* Continue herkenning (async) - Asynchroon initieert continue herkenningsbewerking. De gebruiker registreert zich bij gebeurtenissen en verwerkt verschillende toepassingsstatus. Als u asynchrone [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)continue herkenning wilt stoppen, roept u .

> [!NOTE]
> Meer informatie over het [kiezen van een spraakherkenningsmodus](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Single-shot herkenning

Hier is een voorbeeld van asynchrone [`RecognizeOnceAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet)single-shot herkenning met behulp van:

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

Je moet wat code schrijven om het resultaat te verwerken. In dit voorbeeld [`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet)wordt de volgende beoordeling beoordeeld:

* Hiermee drukt u het herkenningsresultaat af:`ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst is, informeer de gebruiker:`ResultReason.NoMatch`
* Als er een fout is opgetreden, drukt u het foutbericht af:`ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        Console.WriteLine($"    Intent not recognized.");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

### <a name="continuous-recognition"></a>Continue herkenning

Continue herkenning is een beetje meer betrokken dan single-shot erkenning. U moet zich abonneren `Recognizing` `Recognized`op `Canceled` de , , en gebeurtenissen om de erkenning resultaten te krijgen. Om de herkenning te [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)stoppen, moet u bellen. Hier is een voorbeeld van hoe continue herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)de input en het initialiseren van een:

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```
Laten we vervolgens een variabele maken om de status van spraakherkenning te beheren. Om te beginnen, zullen `TaskCompletionSource<int>` we een na de vorige verklaringen te verklaren.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

We abonneren ons op de [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet)evenementen die vanuit de .

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet): Signaal voor gebeurtenissen die tussentijdse herkenningsresultaten bevatten.
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (met vermelding van een geslaagde herkenningspoging).
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet): Signaal voor gebeurtenissen die geannuleerde herkenningsresultaten bevatten (met vermelding van een herkenningspoging die als gevolg is geannuleerd of een directe annuleringsaanvraag of, als alternatief, een transport- of protocolfout).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

Met alles opgezet, kunnen [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)we bellen.

```csharp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// Stops recognition.
await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. Deze modus zorgt ervoor dat de instantie spraakconfig woordbeschrijvingen van zinsstructuren zoals interpunctie interpreteert. Bijvoorbeeld, de uiting "Woont u in de stad vraagteken" zou worden geïnterpreteerd als de tekst "Woont u in de stad?".

Als u de dicteermodus [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet) wilt inschakelen, gebruikt u de methode op uw [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer -taal (of bron). Laten we eens kijken hoe je de invoertaal in het Italiaans zou veranderen. In uw code, [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)vind je , voeg dan deze regel direct eronder.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

De [`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet) accommodatie verwacht een tekenreeks voor taal-landformaat. U elke waarde in de kolom **Locale** vermelden in de lijst met ondersteunde [landlocaties/talen.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>De herkenningsnauwkeurigheid verbeteren

Er zijn een paar manieren om de herkenningsnauwkeurigheid met de Speech SDK te verbeteren. Laten we eens kijken naar phrase lists. Woordgroeplijsten worden gebruikt om bekende zinnen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Enkele woorden of volledige woordgroepen kunnen worden toegevoegd aan een woordgroeplijst. Tijdens de herkenning wordt een vermelding in een woordenlijst gebruikt als een exacte overeenkomst voor de hele woordgroep in de audio is opgenomen. Als er geen exacte overeenkomst wordt gevonden met de woordgroep, wordt de herkenning niet ondersteund.

> [!IMPORTANT]
> De functie Lijst met zinnen is alleen beschikbaar in het Engels.

Als u een woordgroeplijst [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) wilt gebruiken, maakt u [`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet)eerst een object en voegt u vervolgens specifieke woorden en zinnen toe met .

Wijzigingen die [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) van kracht worden op de volgende herkenning of na een heraansluiting met de spraakservice.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Als u uw woordenlijst moet wissen: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties om de herkenningsnauwkeurigheid te verbeteren

Woordlijsten zijn slechts één optie om de herkenningsnauwkeurigheid te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../how-to-custom-speech.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)