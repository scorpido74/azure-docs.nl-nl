---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 8a8647e7f19b55547bbb7eff6f1f3bc1f5282c89
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934532"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de Speech-service wilt aanroepen met behulp van de Speech SDK, moet u een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eindpunt, de host of het autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) kunt initialiseren:

* Met een abonnement: geef een sleutel en de bijbehorende regio door.
* Met een eindpunt: geef een Speech-service-eindpunt door. Een sleutel of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

Laten we eens kijken hoe een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) wordt gemaakt met behulp van een sleutel en regio. Zie de pagina [regio-ondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden.

```csharp
var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-recognizer"></a>Een herkenner initialiseren

Nadat u een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) hebt gemaakt, is de volgende stap het initialiseren van een [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet). Wanneer u een [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) initialiseert, moet u er uw `speechConfig` aan doorgeven. Dit zorgt voor de referenties die de spraakservice nodig heeft om uw aanvraag te valideren.

Als u spraak wilt herkennen met de standaardmicrofoon van uw apparaat, ziet u hier hoe de [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) eruit moet zien:

```csharp
using var recognizer = new SpeechRecognizer(speechConfig);
```

Als u het audio-invoerapparaat wilt opgeven, moet u een [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) maken en de parameter `audioConfig` opgeven bij het initialiseren van uw [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet).

> [!TIP]
> [Meer informatie over het ophalen van de apparaat-id voor het audio-invoerapparaat](../../../how-to-select-audio-input-devices.md).

Voeg eerst de volgende `using`-instructie toe.

```csharp
using Microsoft.CognitiveServices.Speech.Audio;
```

Daarna kunt u als volgt verwijzen naar het `AudioConfig`-object:

```csharp
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Als u een audiobestand wilt opgeven in plaats van een microfoon te gebruiken, moet u nog steeds een `audioConfig` opgeven. Wanneer u echter een [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) maakt in plaats van `FromDefaultMicrophoneInput` aan te roepen, roept u `FromWavFileOutput` aan en geeft u de parameter `filename` door.


```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

## <a name="recognize-speech"></a>Spraak herkennen

De [Recognizer-klasse](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) voor de Speech SDK voor C# maakt een aantal methoden beschikbaar die u voor spraakherkenning kunt gebruiken.

* Eenmalige herkenning (asynchroon): voert herkenning uit in een niet-blokkerende (asynchrone) modus. Hiermee wordt één uiting herkend. Het einde van één uiting wordt bepaald door te luisteren naar stilte aan het einde of tot een maximum van 15 seconden audio is verwerkt.
* Continue herkenning (asynchroon): initieert asynchroon een continue herkenningsbewerking. De gebruiker meldt zich aan bij gebeurtenissen en handelt verschillende toepassingsstatussen af. Als u asynchrone continue herkenning wilt stoppen, roept u [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet) aan.

> [!NOTE]
> Meer informatie over het [kiezen van een spraakherkenningsmodus](../../../how-to-choose-recognition-mode.md).

### <a name="single-shot-recognition"></a>Eenmalige herkenning

Hier volgt een voor beeld van asynchrone eenmalige herkenning met [`RecognizeOnceAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet):

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

U moet code schrijven om het resultaat af te handelen. In dit voorbeeld wordt de [`result.Reason`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason?view=azure-dotnet) geëvalueerd:

* Het herkenningsresultaat afdrukken: `ResultReason.RecognizedSpeech`
* Als er geen herkenningsovereenkomst wordt gevonden, wordt de gebruiker hiervan op de hoogte gesteld: `ResultReason.NoMatch`
* Als er een fout is opgetreden wordt het foutbericht afgedrukt: `ResultReason.Canceled`

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

Continue herkenning is wat ingewikkelder dan eenmalige herkenning. U moet zich abonneren op de gebeurtenissen `Recognizing`, `Recognized`en `Canceled` om de herkenningsresultaten op te halen. Als u de herkenning wilt stoppen, moet u [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet)aanroepen. Hier volgt een voorbeeld van hoe doorlopende herkenning wordt uitgevoerd op een audio-invoerbestand.

Laten we beginnen met het definiëren van de invoer en het initialiseren van een [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet):

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```
Vervolgens maken we een variabele voor het beheren van de status van spraakherkenning. Eerst declareren we een `TaskCompletionSource<int>` na de vorige declaraties.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

We nemen een abonnement op de gebeurtenissen die vanuit de [`SpeechRecognizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer?view=azure-dotnet) worden verzonden.

* [`Recognizing`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-dotnet): Signaal voor gebeurtenissen met tussenliggende herkenningsresultaten.
* [`Recognized`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-dotnet): Signaal voor gebeurtenissen met definitieve herkenningsresultaten (wat een geslaagde herkenningspoging aangeeft).
* [`SessionStopped`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-dotnet): Signaal voor gebeurtenissen die het einde van een herkenningssessie (bewerking) aangeven.
* [`Canceled`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-dotnet): Signaal voor gebeurtenissen met een geannuleerde herkenningsresultaten (waarmee een herkenningspoging wordt aangegeven die is geannuleerd als gevolg van een rechtstreekse annuleringsaanvraag of van een transport- of protocolfout).

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

Nu alles is ingesteld, kunnen we [`StopContinuousRecognitionAsync`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync?view=azure-dotnet) aanroepen.

```csharp
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// Stops recognition.
await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Dicteermodus

Bij het gebruik van continue herkenning kunt u dicteerverwerking inschakelen met behulp van de bijbehorende functie 'dicteren inschakelen'. In deze modus interpreteert de spraakconfiguratie-instantie woordbeschrijvingen van zinselementen zoals interpunctie. Bijvoorbeeld: de uiting 'Woon je hier in de stad vraagteken' wordt geïnterpreteerd als de tekst 'Woon je hier in de stad?'.

Als u de dicteermodus wilt inschakelen, gebruikt u de [`EnableDictation`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet)-methode op uw [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Brontaal wijzigen

Een veelvoorkomende taak voor spraakherkenning is het opgeven van de invoer- (of bron)taal. Laten we eens kijken hoe u de invoertaal wijzigt in Italiaans. Zoek uw [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) in uw code en voeg deze regel daar direct onder toe.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

De eigenschap [`SpeechRecognitionLanguage`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage?view=azure-dotnet) verwacht een indelingstekenreeks voor taal-landinstellingen. U kunt in de lijst met ondersteunde [Landinstellingen en talen](../../../language-support.md) een willekeurige waarde opgeven in de kolom **Landinstelling**.

## <a name="improve-recognition-accuracy"></a>Nauwkeurigheid van de herkenning verbeteren

Er zijn een aantal manieren om de nauwkeurigheid van de herkenning te verbeteren met de Speech SDK. Neem bijvoorbeeld frasenlijsten. Frasenlijsten worden gebruikt om bekende frasen in audiogegevens te identificeren, zoals de naam van een persoon of een specifieke locatie. Er kunnen losse woorden of hele frasen worden toegevoegd aan een frasenlijst. Tijdens de herkenning wordt een vermelding in een frasenlijst gebruikt als er een exacte overeenkomst voor de volledige frase is opgenomen in de audio. Als er geen exacte overeenkomst met de frase wordt gevonden, wordt de herkenning niet ondersteund.

> [!IMPORTANT]
> De frasenlijstfunctie is alleen beschikbaar in het Engels.

Als u een frasenlijst wilt gebruiken, maakt u eerst een [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet)-object, en voegt u vervolgens specifieke woorden en frasen toe met [`AddPhrase`](https://docs.microsoft.com//dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-dotnet).

Wijzigingen in [`PhraseListGrammar`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-dotnet) worden van kracht bij de volgende herkenning of na het opnieuw verbinden met de spraakservice.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Als u uw frasenlijst wilt wissen: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andere opties voor het verbeteren van de nauwkeurigheid van de herkenning

Frasenlijsten zijn maar één optie om de nauwkeurigheid van de herkenning te verbeteren. U kunt ook het volgende doen: 

* [Nauwkeurigheid verbeteren met Custom Speech](../../../how-to-custom-speech.md)
* [Nauwkeurigheid verbeteren met tenantmodellen](../../../tutorial-tenant-model.md)