---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399573"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Java script Speech SDK <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>installeren. Gebruik de volgende instructies, afhankelijk van uw platform:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node. js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Afhankelijk van de doel omgeving gebruikt u bovendien een van de volgende opties:

# <a name="import"></a>[wederinvoer](#tab/import)

```javascript
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

Zie <a href="https://javascript.info/import-export" target="_blank">exporteren en importeren <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>voor meer informatie. `import`

# <a name="require"></a>[vergen](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Zie `require` <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Wat is er vereist? voor meer informatie. <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[uit](#tab/script)

Down load en pak het bestand <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Java <span class="docon docon-navigate-external x-hidden-focus"></span> script Speech SDK</a> *micro soft. cognitiveservices. speech. SDK. bundel. js* uit en plaats het in een map die toegankelijk is voor uw HTML-bestand.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Als u op een webbrowser bent gericht en de `<script>` tag gebruikt; het `sdk` voor voegsel is niet nodig. Het `sdk` voor voegsel is een alias die wordt gebruikt `require` om de module een naam te krijgen.

---

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Als u de spraak service wilt aanroepen met behulp van de Speech SDK [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest), moet u een maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eind punt, de host of het autorisatie token.

> [!NOTE]
> Ongeacht of u spraak herkenning, spraak synthese, vertaling of intentie herkenning uitvoert, maakt u altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)kunt initialiseren:

* Met een abonnement: Geef een sleutel en de bijbehorende regio door.
* Met een eind punt: Pass in een speech service-eind punt. Een sleutel-of autorisatie token is optioneel.
* Met een host: Geef een hostadres door. Een sleutel-of autorisatie token is optioneel.
* Met een autorisatie token: Geef een autorisatie token en de bijbehorende regio door.

In dit voor beeld maakt u een [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) met een abonnements sleutel en-regio. Bekijk de [ondersteunings](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pagina voor regio's om uw regio-id te vinden. U maakt ook een algemene standaard code voor gebruik voor de rest van dit artikel, die u voor verschillende aanpassingen wijzigt.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Spraak op een bestand synthesizer

Vervolgens maakt u een [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) -object, waarmee conversie van tekst naar spraak en uitvoer naar luid sprekers, bestanden of andere uitvoer stromen worden uitgevoerd. De [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) accepteren als params het [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) object dat in de vorige stap is gemaakt en [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) een object dat opgeeft hoe uitvoer resultaten moeten worden verwerkt.

Als u wilt beginnen, `AudioConfig` maakt u een om de uitvoer automatisch `.wav` naar een bestand `fromAudioFileOutput()` te schrijven met behulp van de statische functie.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Vervolgens maakt u een `SpeechSynthesizer` door geven `speechConfig` van uw object `audioConfig` en het object als params. Vervolgens wordt het uitvoeren van een spraak-synthese en het schrijven naar een bestand net `speakTextAsync()` zo eenvoudig als met een teken reeks. Het retour aanroep is een goede plaats om aan `synthesizer.close()`te roepen. deze aanroep is vereist om de synthese goed te laten functioneren.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        }
    },
    error => {
        console.log(error);
        synthesizer.close();
    });
}
```

Voer het programma uit en een gesynthesizerd `.wav` bestand wordt geschreven naar de locatie die u hebt opgegeven. Dit is een goed voor beeld van het meest eenvoudige gebruik, maar vervolgens bekijkt u de uitvoer en het verwerken van de uitvoer respons als een stroom in het geheugen voor het werken met aangepaste scenario's.

## <a name="synthesize-to-speaker-output"></a>Naar de uitvoer van de spreker

In sommige gevallen wilt u mogelijk rechtstreeks de gesynthesizerde spraak rechtstreeks naar een spreker uitvoeren. Als u dit wilt doen, `AudioConfig` moet u `fromDefaultSpeakerOutput()` de functie static gebruiken. Hiermee wordt naar het huidige actieve uitvoer apparaat uitgevoerd.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat ophalen als een in-Memory stroom

Voor veel scenario's in het ontwikkelen van spraak toepassingen hebt u waarschijnlijk de resulterende audio gegevens als een in-Memory stroom nodig, in plaats van rechtstreeks naar een bestand te schrijven. Zo kunt u aangepast gedrag maken, waaronder:

* De resulterende byte matrix samen vatting als Zoek bare stroom voor aangepaste downstream-Services.
* Integreer het resultaat met andere API'S of services.
* Wijzig de audio gegevens, schrijf aangepaste `.wav` kopteksten, enzovoort.

Het is eenvoudig om deze wijziging van het vorige voor beeld uit te voeren. Verwijder eerst het `AudioConfig` blok, omdat u het uitvoer gedrag hand matig vanaf dit punt gaat beheren voor een betere controle. Geef vervolgens `undefined` de `AudioConfig` functie in de `SpeechSynthesizer` constructor door. 

> [!NOTE]
> `undefined` Als u de `AudioConfig`in plaats van het weglaten als in het bovenstaande voor beeld van de spreker, wordt de audio niet standaard afgespeeld op het huidige actieve uitvoer apparaat.

Deze keer slaat u het resultaat op in een [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) variabele. De `SpeechSynthesisResult.audioData` eigenschap retourneert een `ArrayBuffer` van de uitvoer gegevens. U kunt dit `ArrayBuffer` hand matig doen.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

Hier kunt u aangepaste gedragingen implementeren met behulp van `ArrayBuffer` het resulterende object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u audio-uitvoer kenmerken kunt aanpassen, zoals:

* Type audio bestand
* Sample-rate
* Bit-depth

Als u de audio-indeling wilt wijzigen, `speechSynthesisOutputFormat` gebruikt u de `SpeechConfig` eigenschap voor het object. Deze eigenschap verwacht een `enum` van de [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)typen, die u gebruikt om de uitvoer indeling te selecteren. Zie de documentatie van de naslag informatie voor een [lijst met](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) beschik bare audio-indelingen.

Er zijn verschillende opties voor verschillende bestands typen, afhankelijk van uw vereisten. Houd er rekening mee dat RAW-indelingen `Raw24Khz16BitMonoPcm` , zoals geen audio koppen, per definitie zijn. Gebruik RAW-indelingen alleen wanneer u weet dat uw stroomafwaartse implementatie een onbewerkte bitstream kan decoderen of als u van plan bent om headers hand matig te bouwen op basis van de bitdiepte, sample frequentie, aantal kanalen, enzovoort.

In dit voor beeld geeft u een met een hoge betrouw `Riff24Khz16BitMonoPcm` bare RIFF- `speechSynthesisOutputFormat` indeling op `SpeechConfig` door de in te stellen op het object. Net als in het voor beeld in de vorige sectie, kunt `ArrayBuffer` u de audio gegevens ophalen en ermee werken.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
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

Vervolgens moet u de aanvraag voor spraak synthese wijzigen om te verwijzen naar uw XML-bestand. De aanvraag is doorgaans hetzelfde, maar in plaats van de `speakTextAsync()` functie te gebruiken, gebruikt `speakSsmlAsync()`u. Deze functie verwacht een XML-teken reeks, dus eerst maakt u een functie voor het laden van een XML-bestand en retourneert u dit als een teken reeks.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Zie het `readFileSync` <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Bestands systeem<span class="docon docon-navigate-external x-hidden-focus"></span>node. js</a>voor meer informatie over. Hier is het resultaat object precies hetzelfde als de vorige voor beelden.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
