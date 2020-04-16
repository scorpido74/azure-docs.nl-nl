---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.openlocfilehash: d91082ae3c0ae5e501675a06b02e2f55f20ce236
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399573"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript Speech SDK <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>installeren. Gebruik afhankelijk van uw platform de volgende instructies:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Knooppunt.js<span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbrowser<span class="docon docon-navigate-external x-hidden-focus"></span></a>

Bovendien, afhankelijk van de doelomgeving gebruik maken van een van de volgende:

# <a name="import"></a>[Importeren](#tab/import)

```javascript
import { readFileSync } from "fs";
import {
    AudioConfig,
    SpeechConfig,
    SpeechSynthesisOutputFormat,
    SpeechSynthesizer 
} from "microsoft-cognitiveservices-speech-sdk";
```

Zie export `import`en import voor meer informatie over , zie <a href="https://javascript.info/import-export" target="_blank">export en import <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

# <a name="require"></a>[Vereisen](#tab/require)

```javascript
const readFileSync = require("fs").readFileSync;
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Voor meer `require`informatie over , zie <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">wat nodig is? <span class="docon docon-navigate-external x-hidden-focus"></span></a>.


# <a name="script"></a>[uit](#tab/script)

Download en win het <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span> </a> *microsoft.cognitiveservices.speech.sdk.bundle.js-bestand* en plaats deze in een map die toegankelijk is voor uw HTML-bestand.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Als u een webbrowser target en `<script>` de tag gebruikt. het `sdk` voorvoegsel is niet nodig. Het `sdk` voorvoegsel is een `require` alias die wordt gebruikt om de module een naam te geven.

---

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de spraakservice wilt aanroepen met [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest)u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

In dit voorbeeld maakt [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) u een abonnementssleutel en regio. Zie de [pagina regioondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden. U maakt ook een aantal basisboilerplate code te gebruiken voor de rest van dit artikel, die u wijzigen voor verschillende aanpassingen.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Vervolgens maakt u [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) een object dat tekst-naar-spraakconversies en -uitvoer uitvoert naar luidsprekers, bestanden of andere uitvoerstromen. De [`SpeechSynthesizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?view=azure-node-latest) accepteert als params het [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest) object gemaakt in [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) de vorige stap, en een object dat aangeeft hoe de uitvoerresultaten moeten worden behandeld.

Als u wilt `AudioConfig` starten, maakt u `.wav` een om `fromAudioFileOutput()` de uitvoer automatisch naar een bestand te schrijven met de statische functie.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

Vervolgens instantiate `SpeechSynthesizer` een `speechConfig` passeren van `audioConfig` uw object en het object als params. Vervolgens is het uitvoeren van spraaksynthese en het `speakTextAsync()` schrijven naar een bestand net zo eenvoudig als het uitvoeren met een reeks tekst. Het resultaat callback is een `synthesizer.close()`geweldige plek om te bellen , in feite - deze oproep is nodig om synthese correct te laten functioneren.

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

Voer het programma uit `.wav` en er wordt een gesynthetiseerd bestand geschreven naar de locatie die u hebt opgegeven. Dit is een goed voorbeeld van het meest elementaire gebruik, maar vervolgens kijkt u naar het aanpassen van de uitvoer en het verwerken van de uitvoerrespons als een in-memory stream voor het werken met aangepaste scenario's.

## <a name="synthesize-to-speaker-output"></a>Synthetiseren naar luidsprekeruitvoer

In sommige gevallen u gesynthetiseerde spraak rechtstreeks naar een spreker leiden. Om dit te doen, `AudioConfig` instantiate het gebruik van de `fromDefaultSpeakerOutput()` statische functie. Hiermee wordt naar het huidige actieve uitvoerapparaat uitgevoerd.

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

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat krijgen als een in-memory stream

Voor veel scenario's in de ontwikkeling van spraaktoepassingen hebt u waarschijnlijk de resulterende audiogegevens nodig als een in-memory stream in plaats van rechtstreeks naar een bestand te schrijven. Hiermee u aangepast gedrag op te bouwen, waaronder:

* De resulterende bytearray abstraheren als een zoekbare stream voor aangepaste downstreamservices.
* Integreer het resultaat met andere API's of services.
* Wijzig de audiogegevens, `.wav` schrijf aangepaste kopteksten, enz.

Het is eenvoudig om deze wijziging aan te brengen ten opzichte van het vorige voorbeeld. Verwijder eerst `AudioConfig` het blok, omdat u het uitvoergedrag vanaf dit punt handmatig beheert voor meer controle. Ga `undefined` dan `AudioConfig` voor `SpeechSynthesizer` de in de constructor. 

> [!NOTE]
> Het `undefined` doorgeven `AudioConfig`van de , in plaats van weglaten zoals in de luidspreker uitgang voorbeeld hierboven, zal niet standaard afspelen van de audio op de huidige actieve uitvoer apparaat.

Deze keer slaat u het [`SpeechSynthesisResult`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?view=azure-node-latest) resultaat op een variabele. De `SpeechSynthesisResult.audioData` eigenschap `ArrayBuffer` retourneert een van de uitvoergegevens. U hier `ArrayBuffer` handmatig mee werken.

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

Vanaf hier u elk aangepast `ArrayBuffer` gedrag implementeren met behulp van het resulterende object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u audio-uitvoerkenmerken aanpassen, waaronder:

* Audiobestandstype
* Steekproefsnelheid
* Bitdiepte

Als u de audio-indeling `speechSynthesisOutputFormat` wilt `SpeechConfig` wijzigen, gebruikt u de eigenschap op het object. Deze eigenschap `enum` verwacht [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)een type, dat u gebruikt om de uitvoerindeling te selecteren. Zie de referentiedocumenten voor een [lijst met beschikbare audio-indelingen.](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest)

Er zijn verschillende opties voor verschillende bestandstypen, afhankelijk van uw vereisten. Houd er rekening mee `Raw24Khz16BitMonoPcm` dat raw-indelingen, zoals, per definitie geen audiokopteksten bevatten. Gebruik raw-indelingen alleen wanneer u weet dat uw downstream-implementatie een ruwe bitstream kan decoderen, of als u van plan bent om handmatig headers te bouwen op basis van bitdiepte, sample-rate, aantal kanalen, enz.

In dit voorbeeld geeft u een hifi-RIFF-indeling `Riff24Khz16BitMonoPcm` op door de `speechSynthesisOutputFormat` indeling op het object in te `SpeechConfig` stellen. Net als in het voorbeeld in `ArrayBuffer` de vorige sectie, krijgt u de audiogegevens en interactie met het.

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

Als u uw programma `.wav` opnieuw uitvoert, wordt een bestand naar het opgegeven pad geschreven.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML gebruiken om spraakkenmerken aan te passen

Met De Opmaaktaal voor spraaksynthese (SSML) u de toonhoogte, uitspraak, spreeksnelheid, volume en meer van de tekst-naar-spraak-uitvoer verfijnen door uw aanvragen vanuit een XML-schema in te dienen. Deze sectie toont een paar praktische gebruiksvoorbeelden, maar voor een meer gedetailleerde gids, zie het [SSML-artikel how-to.](../../../speech-synthesis-markup.md)

Als u SSML wilt gebruiken voor aanpassing, brengt u een eenvoudige wijziging door die de stem schakelt.
Maak eerst een nieuw XML-bestand voor de SSML-config `ssml.xml`in uw hoofdprojectmap in dit voorbeeld. Het hoofdelement `<speak>`is altijd, en `<voice>` het omwikkelen van de `name` tekst in een element u de stem te veranderen met behulp van de param. In dit voorbeeld wordt de stem gewijzigd in een mannelijke Engelse (UK) stem. Merk op dat deze stem is een **standaard** stem, die verschillende prijzen en beschikbaarheid dan **neurale** stemmen heeft. Bekijk de [volledige lijst](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices) met ondersteunde **standaardstemmen.**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

Vervolgens moet u het verzoek voor spraaksynthese wijzigen om naar uw XML-bestand te verwijzen. De aanvraag is meestal hetzelfde, `speakTextAsync()` maar in `speakSsmlAsync()`plaats van de functie te gebruiken, gebruikt u . Deze functie verwacht een XML-tekenreeks, dus eerst maakt u een functie om een XML-bestand te laden en als tekenreeks te retourneren.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

Zie `readFileSync` <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js-bestandssysteem<span class="docon docon-navigate-external x-hidden-focus"></span></a>voor meer informatie over , zie Node.js. Vanaf hier is het resultaatobject precies hetzelfde als eerdere voorbeelden.

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

De uitvoer werkt, maar er een paar eenvoudige extra wijzigingen die u aanbrengen om het geluid natuurlijker. De algehele spreeksnelheid is een beetje te snel, dus we voegen een `<prosody>` tag toe en verlagen de snelheid tot **90%** van het standaardtarief. Bovendien is de pauze na de komma in de zin een beetje te kort en onnatuurlijk klinken. Als u dit probleem `<break>` wilt oplossen, voegt u een tag toe om de spraak uit te stellen en stelt u de tijdparam in op **200 ms.** Voer de synthese opnieuw uit om te zien hoe deze aanpassingen de uitvoer beïnvloedden.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>Neurale stemmen

Neurale stemmen zijn spraaksynthese algoritmen aangedreven door diepe neurale netwerken. Bij het gebruik van een neurale stem, gesynthetiseerde spraak is bijna niet te onderscheiden van de menselijke opnames. Met de mens-achtige natuurlijke prosodie en duidelijke articulatie van woorden, neurale stemmen aanzienlijk verminderen luistervermoeidheid wanneer gebruikers communiceren met AI-systemen.

Als u wilt overschakelen `name` naar een neurale stem, verandert u de naar een van de [neurale stemopties.](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) Voeg vervolgens een XML-naamruimte toe voor `mstts` `<mstts:express-as>` en wikkel de tekst in de tag. Gebruik `style` de param om de spreekstijl aan te passen. In dit `cheerful`voorbeeld wordt het `customerservice` `chat` gebruik gebruikt, maar probeer het in te stellen of om het verschil in spreekstijl te zien.

> [!IMPORTANT]
> Neurale stemmen worden **alleen** ondersteund voor spraakbronnen gemaakt in *Oost-VS,* *Zuidoost-Azië*en *West-Europa* regio's.

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
