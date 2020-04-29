---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: be60a2f371148fabf73fc7fcdce114295775d71c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80986252"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een spraak service-abonnement hebt. Als u geen account en abonnement hebt, [kunt u de spraak service gratis uitproberen](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets kunt doen, moet u de Speech SDK installeren.

```Python
pip install azure-cognitiveservices-speech
```

Als u zich in macOS bevindt en er problemen met de installatie optreden, moet u deze opdracht mogelijk eerst uitvoeren.

```Python
python3 -m pip install --upgrade pip
```

Nadat de Speech SDK is geïnstalleerd, voegt u de volgende import instructies toe boven aan het script.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Als u de spraak service wilt aanroepen met behulp van de Speech SDK [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python), moet u een maken. Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en de bijbehorende regio, het eind punt, de host of het autorisatie token.

> [!NOTE]
> Ongeacht of u spraak herkenning, spraak synthese, vertaling of intentie herkenning uitvoert, maakt u altijd een configuratie.

Er zijn een paar manieren waarop u een [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)kunt initialiseren:

* Met een abonnement: Geef een sleutel en de bijbehorende regio door.
* Met een eind punt: Pass in een speech service-eind punt. Een sleutel-of autorisatie token is optioneel.
* Met een host: Geef een hostadres door. Een sleutel-of autorisatie token is optioneel.
* Met een autorisatie token: Geef een autorisatie token en de bijbehorende regio door.

In dit voor beeld maakt u een [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) met een abonnements sleutel en-regio. Bekijk de [ondersteunings](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) pagina voor regio's om uw regio-id te vinden.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Spraak op een bestand synthesizer

Vervolgens maakt u een [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) -object, waarmee conversie van tekst naar spraak en uitvoer naar luid sprekers, bestanden of andere uitvoer stromen worden uitgevoerd. De [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) accepteren als params het [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) object dat in de vorige stap is gemaakt en [`AudioOutputConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python) een object dat opgeeft hoe uitvoer resultaten moeten worden verwerkt.

Als u wilt beginnen, `AudioOutputConfig` maakt u een om de uitvoer automatisch `.wav` naar een bestand te `filename` schrijven met behulp van de constructor-para meter.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Exemplaar vervolgens een `SpeechSynthesizer` door uw `speech_config` object en het `audio_config` object door te geven als params. Vervolgens wordt het uitvoeren van een spraak-synthese en het schrijven naar een bestand net `speak_text_async()` zo eenvoudig als met een teken reeks.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Voer het programma uit en een gesynthesizerd `.wav` bestand wordt geschreven naar de locatie die u hebt opgegeven. Dit is een goed voor beeld van het meest eenvoudige gebruik, maar vervolgens bekijkt u de uitvoer en het verwerken van de uitvoer respons als een stroom in het geheugen voor het werken met aangepaste scenario's.

## <a name="synthesize-to-speaker-output"></a>Naar de uitvoer van de spreker

In sommige gevallen wilt u mogelijk rechtstreeks de gesynthesizerde spraak rechtstreeks naar een spreker uitvoeren. Gebruik hiervoor het voor beeld in de vorige sectie, maar wijzig de `AudioOutputConfig` waarde door de `filename` para meter te verwijderen en in `use_default_speaker=True`te stellen. Hiermee wordt naar het huidige actieve uitvoer apparaat uitgevoerd.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat ophalen als een in-Memory stroom

Voor veel scenario's in het ontwikkelen van spraak toepassingen hebt u waarschijnlijk de resulterende audio gegevens als een in-Memory stroom nodig, in plaats van rechtstreeks naar een bestand te schrijven. Zo kunt u aangepast gedrag maken, waaronder:

* De resulterende byte matrix samen vatting als Zoek bare stroom voor aangepaste downstream-Services.
* Integreer het resultaat met andere API'S of services.
* Wijzig de audio gegevens, schrijf aangepaste `.wav` kopteksten, enzovoort.

Het is eenvoudig om deze wijziging van het vorige voor beeld uit te voeren. Eerst verwijdert u de `AudioConfig`, omdat u het uitvoer gedrag hand matig vanaf dit punt gaat beheren voor een betere controle. Geef vervolgens `None` de `AudioConfig` functie in de `SpeechSynthesizer` constructor door. 

> [!NOTE]
> `None` Als u de `AudioConfig`in plaats van het weglaten als in het bovenstaande voor beeld van de spreker, wordt de audio niet standaard afgespeeld op het huidige actieve uitvoer apparaat.

Deze keer slaat u het resultaat op in een [`SpeechSynthesisResult`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?view=azure-python) variabele. De `audio_data` eigenschap bevat een `bytes` object van de uitvoer gegevens. U kunt met dit object hand matig werken of u kunt de [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) klasse gebruiken om de stroom in het geheugen te beheren. In dit voor beeld gebruikt u `AudioDataStream` de constructor om een stroom van het resultaat op te halen.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Hier kunt u aangepaste gedragingen implementeren met behulp van `stream` het resulterende object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u audio-uitvoer kenmerken kunt aanpassen, zoals:

* Type audio bestand
* Sample-rate
* Bit-depth

Als u de audio-indeling wilt wijzigen, `set_speech_synthesis_output_format()` gebruikt u de `SpeechConfig` functie voor het object. Deze functie verwacht een `enum` van de [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)typen, die u gebruikt om de uitvoer indeling te selecteren. Zie de documentatie van de naslag informatie voor een [lijst met](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python) beschik bare audio-indelingen.

Er zijn verschillende opties voor verschillende bestands typen, afhankelijk van uw vereisten. Houd er rekening mee dat RAW-indelingen `Raw24Khz16BitMonoPcm` , zoals geen audio koppen, per definitie zijn. Gebruik RAW-indelingen alleen wanneer u weet dat uw stroomafwaartse implementatie een onbewerkte bitstream kan decoderen of als u van plan bent om headers hand matig te bouwen op basis van de bitdiepte, sample frequentie, aantal kanalen, enzovoort.

In dit voor beeld geeft u een met een hoge betrouw `Riff24Khz16BitMonoPcm` bare RIFF- `SpeechSynthesisOutputFormat` indeling op `SpeechConfig` door de in te stellen op het object. Net als in het voor beeld in de vorige sectie gebruikt [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) u voor het ophalen van een stroom in het geheugen van het resultaat en schrijft u deze naar een bestand.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Als u het programma opnieuw uitvoert, wordt `.wav` een aangepast bestand naar het opgegeven pad geschreven.

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

Vervolgens moet u de aanvraag voor spraak synthese wijzigen om te verwijzen naar uw XML-bestand. De aanvraag is doorgaans hetzelfde, maar in plaats van de `speak_text_async()` functie te gebruiken, gebruikt `speak_ssml_async()`u. Deze functie verwacht een XML-teken reeks, dus u moet eerst uw SSML-configuratie lezen als een teken reeks. Hier is het resultaat object precies hetzelfde als de vorige voor beelden.

> [!NOTE]
> Als u `ssml_string` aan `ï»¿` het begin van de teken reeks staat, moet u de stuk lijst opmaak verwijderen of wordt er een fout geretourneerd door de service. U doet dit door de `encoding` para meter als volgt in `open("ssml.xml", "r", encoding="utf-8-sig")`te stellen:.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
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
