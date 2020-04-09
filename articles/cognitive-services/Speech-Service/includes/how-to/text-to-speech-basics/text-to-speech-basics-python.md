---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: be60a2f371148fabf73fc7fcdce114295775d71c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986252"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren.

```Python
pip install azure-cognitiveservices-speech
```

Als u macOS bevindt en installatieproblemen ondervindt, moet u deze opdracht mogelijk eerst uitvoeren.

```Python
python3 -m pip install --upgrade pip
```

Nadat de SpraakSDK is geïnstalleerd, neemt u de volgende importinstructies boven aan uw script op.

```Python
from azure.cognitiveservices.speech import AudioDataStream, SpeechConfig, SpeechSynthesizer, SpeechSynthesisOutputFormat
from azure.cognitiveservices.speech.audio import AudioOutputConfig
```

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de spraakservice wilt aanroepen met [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

In dit voorbeeld maakt [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) u een abonnementssleutel en regio. Zie de [pagina regioondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden.

```python
speech_config = SpeechConfig(subscription="YourSubscriptionKey", region="YourServiceRegion")
```

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Vervolgens maakt u [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) een object dat tekst-naar-spraakconversies en -uitvoer uitvoert naar luidsprekers, bestanden of andere uitvoerstromen. De [`SpeechSynthesizer`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python) accepteert als params het [`SpeechConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python) object gemaakt in [`AudioOutputConfig`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audiooutputconfig?view=azure-python) de vorige stap, en een object dat aangeeft hoe de uitvoerresultaten moeten worden behandeld.

Als u wilt `AudioOutputConfig` beginnen, maakt u `.wav` een om `filename` de uitvoer automatisch naar een bestand te schrijven met behulp van de constructorparam.

```python
audio_config = AudioOutputConfig(filename="path/to/write/file.wav")
```

Vervolgens instantiate `SpeechSynthesizer` een door `speech_config` het `audio_config` passeren van uw object en het object als params. Vervolgens is het uitvoeren van spraaksynthese en het `speak_text_async()` schrijven naar een bestand net zo eenvoudig als het uitvoeren met een reeks tekst.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
synthesizer.speak_text_async("A simple test to write to a file.")
```

Voer het programma uit `.wav` en er wordt een gesynthetiseerd bestand geschreven naar de locatie die u hebt opgegeven. Dit is een goed voorbeeld van het meest elementaire gebruik, maar vervolgens kijkt u naar het aanpassen van de uitvoer en het verwerken van de uitvoerrespons als een in-memory stream voor het werken met aangepaste scenario's.

## <a name="synthesize-to-speaker-output"></a>Synthetiseren naar luidsprekeruitvoer

In sommige gevallen u gesynthetiseerde spraak rechtstreeks naar een spreker leiden. Gebruik hiervoor het voorbeeld in de vorige sectie, maar `filename` wijzigt het `use_default_speaker=True`door de `AudioOutputConfig` param te verwijderen en stel in . Hiermee wordt naar het huidige actieve uitvoerapparaat uitgevoerd.

```python
audio_config = AudioOutputConfig(use_default_speaker=True)
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat krijgen als een in-memory stream

Voor veel scenario's in de ontwikkeling van spraaktoepassingen hebt u waarschijnlijk de resulterende audiogegevens nodig als een in-memory stream in plaats van rechtstreeks naar een bestand te schrijven. Hiermee u aangepast gedrag op te bouwen, waaronder:

* De resulterende bytearray abstraheren als een zoekbare stream voor aangepaste downstreamservices.
* Integreer het resultaat met andere API's of services.
* Wijzig de audiogegevens, `.wav` schrijf aangepaste kopteksten, enz.

Het is eenvoudig om deze wijziging aan te brengen ten opzichte van het vorige voorbeeld. Verwijder eerst `AudioConfig`de , als u het uitvoergedrag handmatig te beheren vanaf dit punt verder voor meer controle. Ga `None` dan `AudioConfig` voor `SpeechSynthesizer` de in de constructor. 

> [!NOTE]
> Het `None` doorgeven `AudioConfig`van de , in plaats van weglaten zoals in de luidspreker uitgang voorbeeld hierboven, zal niet standaard afspelen van de audio op de huidige actieve uitvoer apparaat.

Deze keer slaat u het [`SpeechSynthesisResult`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisresult?view=azure-python) resultaat op een variabele. De `audio_data` eigenschap `bytes` bevat een object van de uitvoergegevens. U handmatig met dit object werken [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) of u de klasse gebruiken om de in-memory stream te beheren. In dit voorbeeld `AudioDataStream` gebruikt u de constructor om een stroom uit het resultaat te halen.

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)
result = synthesizer.speak_text_async("Getting the response as an in-memory stream.").get()
stream = AudioDataStream(result)
```

Vanaf hier u elk aangepast `stream` gedrag implementeren met behulp van het resulterende object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u audio-uitvoerkenmerken aanpassen, waaronder:

* Audiobestandstype
* Steekproefsnelheid
* Bitdiepte

Als u de audio-indeling `set_speech_synthesis_output_format()` wilt `SpeechConfig` wijzigen, gebruikt u de functie op het object. Deze functie `enum` verwacht [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)een type, dat u gebruikt om de uitvoerindeling te selecteren. Zie de referentiedocumenten voor een [lijst met beschikbare audio-indelingen.](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)

Er zijn verschillende opties voor verschillende bestandstypen, afhankelijk van uw vereisten. Houd er rekening mee `Raw24Khz16BitMonoPcm` dat raw-indelingen, zoals, per definitie geen audiokopteksten bevatten. Gebruik raw-indelingen alleen wanneer u weet dat uw downstream-implementatie een ruwe bitstream kan decoderen, of als u van plan bent om handmatig headers te bouwen op basis van bitdiepte, sample-rate, aantal kanalen, enz.

In dit voorbeeld geeft u een hifi-RIFF-indeling `Riff24Khz16BitMonoPcm` op door de `SpeechSynthesisOutputFormat` indeling op het object in te `SpeechConfig` stellen. Net als in het voorbeeld in [`AudioDataStream`](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audiodatastream?view=azure-python) de vorige sectie, gebruikt u om een in-memory stream van het resultaat te krijgen en vervolgens naar een bestand te schrijven.


```python
speech_config.set_speech_synthesis_output_format(SpeechSynthesisOutputFormat["Riff24Khz16BitMonoPcm"])
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

result = synthesizer.speak_text_async("Customizing audio output format.").get()
stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
```

Als u uw programma `.wav` opnieuw uitvoert, wordt een aangepast bestand naar het opgegeven pad geschreven.

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

Vervolgens moet u het verzoek voor spraaksynthese wijzigen om naar uw XML-bestand te verwijzen. De aanvraag is meestal hetzelfde, `speak_text_async()` maar in `speak_ssml_async()`plaats van de functie te gebruiken, gebruikt u . Deze functie verwacht een XML-tekenreeks, zodat u eerst uw SSML-config als tekenreeks leest. Vanaf hier is het resultaatobject precies hetzelfde als eerdere voorbeelden.

> [!NOTE]
> Als `ssml_string` uw `ï»¿` gegevens aan het begin van de tekenreeks worden weergegeven, moet u de bomnotatie uitdeinen of geeft de service een fout. U doet dit `encoding` door de `open("ssml.xml", "r", encoding="utf-8-sig")`parameter als volgt in te stellen: .

```python
synthesizer = SpeechSynthesizer(speech_config=speech_config, audio_config=None)

ssml_string = open("ssml.xml", "r").read()
result = synthesizer.speak_ssml_async(ssml_string).get()

stream = AudioDataStream(result)
stream.save_to_wav_file("path/to/write/file.wav")
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
