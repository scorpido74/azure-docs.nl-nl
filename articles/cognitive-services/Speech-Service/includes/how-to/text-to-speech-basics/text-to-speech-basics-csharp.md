---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: 8679d6d4c8ff0a6abdf045187c284ca65f43ee7e
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80986259"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren. Gebruik de volgende instructies, afhankelijk van uw platform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET core<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voor beelden in dit artikel wilt uitvoeren, `using` moet u de volgende instructies toevoegen boven aan het script.

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Als u de spraak service wilt aanroepen met behulp van de Speech SDK [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet), moet u een maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eind punt, de host of het autorisatie token.

> [!NOTE]
> Ongeacht of u spraak herkenning, spraak synthese, vertaling of intentie herkenning uitvoert, maakt u altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)kunt initialiseren:

* Met een abonnement: Geef een sleutel en de bijbehorende regio door.
* Met een eind punt: Pass in een speech service-eind punt. Een sleutel-of autorisatie token is optioneel.
* Met een host: Geef een hostadres door. Een sleutel-of autorisatie token is optioneel.
* Met een autorisatie token: Geef een autorisatie token en de bijbehorende regio door.

In dit voor beeld maakt u een [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) met een abonnements sleutel en-regio. Bekijk de [ondersteunings](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pagina voor regio's om uw regio-id te vinden. U maakt ook een algemene standaard code voor gebruik voor de rest van dit artikel, die u voor verschillende aanpassingen wijzigt.

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>Spraak op een bestand synthesizer

Vervolgens maakt u een [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) -object, waarmee conversie van tekst naar spraak en uitvoer naar luid sprekers, bestanden of andere uitvoer stromen worden uitgevoerd. De [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) accepteren als params het [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) object dat in de vorige stap is gemaakt en [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) een object dat opgeeft hoe uitvoer resultaten moeten worden verwerkt.

Als u wilt beginnen, `AudioConfig` maakt u een om de uitvoer automatisch `.wav` naar een bestand te `FromWavFileOutput()` schrijven, met behulp van de `using` functie en een exemplaar te maken met een-instructie. Met `using` een instructie in deze context worden niet-beheerde resources automatisch verwijderd en wordt het object na verwijdering buiten gebruik gesteld.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

Vervolgens maakt u een `SpeechSynthesizer` exemplaar van `using` een met een andere-instructie. Geef uw `config` object en het `audioConfig` object door als params. Vervolgens wordt het uitvoeren van een spraak-synthese en het schrijven naar een bestand net `SpeakTextAsync()` zo eenvoudig als met een teken reeks.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

Voer het programma uit en een gesynthesizerd `.wav` bestand wordt geschreven naar de locatie die u hebt opgegeven. Dit is een goed voor beeld van het meest eenvoudige gebruik, maar vervolgens bekijkt u de uitvoer en het verwerken van de uitvoer respons als een stroom in het geheugen voor het werken met aangepaste scenario's.

## <a name="synthesize-to-speaker-output"></a>Naar de uitvoer van de spreker

In sommige gevallen wilt u mogelijk rechtstreeks de gesynthesizerde spraak rechtstreeks naar een spreker uitvoeren. U kunt dit doen door de `AudioConfig` para meter weg te laten `SpeechSynthesizer` bij het maken van de in het bovenstaande voor beeld. Hiermee wordt naar het huidige actieve uitvoer apparaat uitgevoerd.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat ophalen als een in-Memory stroom

Voor veel scenario's in het ontwikkelen van spraak toepassingen hebt u waarschijnlijk de resulterende audio gegevens als een in-Memory stroom nodig, in plaats van rechtstreeks naar een bestand te schrijven. Zo kunt u aangepast gedrag maken, waaronder:

* De resulterende byte matrix samen vatting als Zoek bare stroom voor aangepaste downstream-Services.
* Integreer het resultaat met andere API'S of services.
* Wijzig de audio gegevens, schrijf aangepaste `.wav` kopteksten, enzovoort.

Het is eenvoudig om deze wijziging van het vorige voor beeld uit te voeren. Verwijder eerst het `AudioConfig` blok, omdat u het uitvoer gedrag hand matig vanaf dit punt gaat beheren voor een betere controle. Geef vervolgens `null` de `AudioConfig` functie in de `SpeechSynthesizer` constructor door. 

> [!NOTE]
> `null` Als u de `AudioConfig`in plaats van het weglaten als in het bovenstaande voor beeld van de spreker, wordt de audio niet standaard afgespeeld op het huidige actieve uitvoer apparaat.

Deze keer slaat u het resultaat op in een [`SpeechSynthesisResult`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-dotnet) variabele. De `AudioData` eigenschap bevat een `byte []` van de uitvoer gegevens. U kunt dit `byte []` hand matig doen, of u kunt de [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) klasse gebruiken om de stroom in het geheugen te beheren. In dit voor beeld gebruikt u `AudioDataStream.FromResult()` de statische functie om een stroom van het resultaat op te halen.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

Hier kunt u aangepaste gedragingen implementeren met behulp van `stream` het resulterende object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u audio-uitvoer kenmerken kunt aanpassen, zoals:

* Type audio bestand
* Sample-rate
* Bit-depth

Als u de audio-indeling wilt wijzigen, `SetSpeechSynthesisOutputFormat()` gebruikt u de `SpeechConfig` functie voor het object. Deze functie verwacht een `enum` van de [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)typen, die u gebruikt om de uitvoer indeling te selecteren. Zie de documentatie van de naslag informatie voor een [lijst met](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) beschik bare audio-indelingen.

Er zijn verschillende opties voor verschillende bestands typen, afhankelijk van uw vereisten. Houd er rekening mee dat RAW-indelingen `Raw24Khz16BitMonoPcm` , zoals geen audio koppen, per definitie zijn. Gebruik RAW-indelingen alleen wanneer u weet dat uw stroomafwaartse implementatie een onbewerkte bitstream kan decoderen of als u van plan bent om headers hand matig te bouwen op basis van de bitdiepte, sample frequentie, aantal kanalen, enzovoort.

In dit voor beeld geeft u een met een hoge betrouw `Riff24Khz16BitMonoPcm` bare RIFF- `SpeechSynthesisOutputFormat` indeling op `SpeechConfig` door de in te stellen op het object. Net als in het voor beeld in de vorige sectie gebruikt [`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) u voor het ophalen van een stroom in het geheugen van het resultaat en schrijft u deze naar een bestand.

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

Als u het programma opnieuw uitvoert, `.wav` wordt een bestand naar het opgegeven pad geschreven.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML gebruiken om spraak kenmerken aan te passen

Met SSML (Speech synthese Markup Language) kunt u de hoogte, de uitspraak, de spreek snelheid, het volume en meer van de tekst-naar-spraak-uitvoer nauw keuriger instellen door uw aanvragen van een XML-schema te verzenden. In deze sectie vindt u enkele praktische gebruiks voorbeelden, maar voor een meer gedetailleerde hand leiding raadpleegt u het [artikel SSML How-to](../../../speech-synthesis-markup.md).

Als u SSML wilt gaan gebruiken voor aanpassing, maakt u een eenvoudige wijziging die de stem wijzigt.
Maak eerst een nieuw XML-bestand voor de SSML-configuratie in de hoofdmap van het project, in `ssml.xml`dit voor beeld. Het hoofd element is altijd `<speak>`en door de tekst in een `<voice>` -element op te maken, kunt u de stem `name` wijzigen met behulp van de para meter. In dit voor beeld wordt de stem gewijzigd in een Engelse (UK)-stem. Houd er rekening mee dat deze stem een **standaard** stem is die verschillende prijzen en beschik baarheid heeft dan **Neural** stemmen. Bekijk de [volledige lijst](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) met ondersteunde **standaard** stemmen.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Vervolgens moet u de aanvraag voor spraak synthese wijzigen om te verwijzen naar uw XML-bestand. De aanvraag is doorgaans hetzelfde, maar in plaats van de `SpeakTextAsync()` functie te gebruiken, gebruikt `SpeakSsmlAsync()`u. Deze functie verwacht een XML-teken reeks, dus u moet uw SSML config eerst laden als `File.ReadAllText()`een teken reeks met behulp van. Hier is het resultaat object precies hetzelfde als de vorige voor beelden.

> [!NOTE]
> Als u Visual Studio gebruikt, wordt uw XML-bestand waarschijnlijk niet standaard door uw build-configuratie gevonden. U kunt dit oplossen door met de rechter muisknop op het XML-bestand te klikken en **Eigenschappen**te selecteren. Wijzig de **opbouw actie** in *inhoud*en wijzig de **kopie naar de uitvoermap** om altijd te *kopiëren*.

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

De uitvoer werkt, maar er zijn enkele eenvoudige wijzigingen die u kunt aanbrengen om de IT-mede werkers meer natuurlijker te maken. De totale spreek snelheid is te snel. Daarom gaan we een `<prosody>` tag toevoegen en de snelheid tot **90%** van de standaard snelheid verlagen. Bovendien is de pauze na de komma in de zin een beetje te kort en onnatuurlijk. U kunt dit probleem oplossen door een `<break>` tag toe te voegen om de spraak vertraging te vertraagen en de tijd parameter in te stellen op **200ms**. Voer de synthese opnieuw uit om te zien hoe deze aanpassingen van invloed zijn op de uitvoer.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neural stemmen

Neural stemmen zijn spraak synthese-algoritmen die worden aangedreven door diepe Neural-netwerken. Wanneer u een Neural-stem gebruikt, is de gesynthesizerde spraak bijna niet van de menselijke opnamen onderscheiden. Met het menselijke net zoals natuurlijke prosody en heldere afbakening van woorden, verlaagt Neural stemmen aanzienlijk op het Luis teren van vermoeidheid wanneer gebruikers met AI-systemen communiceren.

Als u wilt overschakelen naar een neurale stem `name` , wijzigt u de in een van de [Neural-stem opties](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices). Voeg vervolgens een XML-naam ruimte `mstts`toe voor en verpakken de tekst `<mstts:express-as>` in de tag. Gebruik de `style` para meter om de spreek stijl aan te passen. In dit voor `cheerful`beeld wordt gebruikt, maar u `customerservice` kunt `chat` deze instelling instellen op of om het verschil in de sprekende stijl te bekijken.

> [!IMPORTANT]
> Neural stemmen worden **alleen** ondersteund voor spraak bronnen die zijn gemaakt in *VS-Oost*, *Zuid-Azië-Oost*en *Europa-West* regio's.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
