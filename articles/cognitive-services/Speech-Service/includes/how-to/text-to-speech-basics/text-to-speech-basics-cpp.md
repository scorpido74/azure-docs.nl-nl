---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.openlocfilehash: c5d954cc2bdda0b1fcb67801fa948e1f56fb0364
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986238"
---
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account- en spraakserviceabonnement hebt. Als u geen account en abonnement hebt, [probeert u de spraakservice gratis.](../../../get-started.md)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voordat u iets doen, moet u de Speech SDK installeren. Gebruik afhankelijk van uw platform de volgende instructies:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">Macos<span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Windows<span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>Afhankelijkheden importeren

Als u de voorbeelden in dit artikel `using` wilt uitvoeren, neemt u de volgende import en instructies boven aan uw script op.

```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <speechapi_cxx.h>

using namespace std;
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;
```

## <a name="create-a-speech-configuration"></a>Een spraakconfiguratie maken

Als u de spraakservice wilt aanroepen met [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)de Spraak-SDK, moet u een . . Deze klasse bevat informatie over uw abonnement, zoals uw sleutel en bijbehorende regio, eindpunt, host of autorisatietoken.

> [!NOTE]
> Ongeacht of u spraakherkenning, spraaksynthese, vertaling of intentieherkenning uitvoert, u maakt altijd een configuratie.

Er zijn een paar manieren waarop [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)u een initialiseren:

* Met een abonnement: pas in een sleutel en de bijbehorende regio.
* Met een eindpunt: geef een eindpunt van de spraakservice door. Een sleutel- of autorisatietoken is optioneel.
* Met een host: geef een hostadres door. Een sleutel- of autorisatietoken is optioneel.
* Met een autorisatietoken: geef een autorisatietoken en de bijbehorende regio door.

In dit voorbeeld maakt [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) u een abonnementssleutel en regio. Zie de [pagina regioondersteuning](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) om uw regio-id te vinden. U maakt ook een aantal basisboilerplate code te gebruiken voor de rest van dit artikel, die u wijzigen voor verschillende aanpassingen.

```cpp
int wmain()
{
    try
    {
        synthesizeSpeech();
    }
    catch (exception e)
    {
        cout << e.what();
    }
    return 0;
}
    
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
}
```

## <a name="synthesize-speech-to-a-file"></a>Spraak synthetiseren naar een bestand

Vervolgens maakt u [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) een object dat tekst-naar-spraakconversies en -uitvoer uitvoert naar luidsprekers, bestanden of andere uitvoerstromen. De [`SpeechSynthesizer`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer) accepteert als params het [`SpeechConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig) object gemaakt in [`AudioConfig`](https://docs.microsoft.com/cpp/cognitive-services/speech/audio-audioconfig) de vorige stap, en een object dat aangeeft hoe de uitvoerresultaten moeten worden behandeld.

Als u wilt `AudioConfig` starten, maakt u `.wav` een om `FromWavFileOutput()` de uitvoer automatisch naar een bestand te schrijven met behulp van de functie.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
}
```

Vervolgens instantiate `SpeechSynthesizer`een , `config` het `audioConfig` passeren van uw object en het object als params. Vervolgens is het uitvoeren van spraaksynthese en het `SpeakTextAsync()` schrijven naar een bestand net zo eenvoudig als het uitvoeren met een reeks tekst.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto audioConfig = AudioConfig::FromWavFileOutput("path/to/write/file.wav");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, audioConfig);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
}
```

Voer het programma uit `.wav` en er wordt een gesynthetiseerd bestand geschreven naar de locatie die u hebt opgegeven. Dit is een goed voorbeeld van het meest elementaire gebruik, maar vervolgens kijkt u naar het aanpassen van de uitvoer en het verwerken van de uitvoerrespons als een in-memory stream voor het werken met aangepaste scenario's.

## <a name="synthesize-to-speaker-output"></a>Synthetiseren naar luidsprekeruitvoer

In sommige gevallen u gesynthetiseerde spraak rechtstreeks naar een spreker leiden. Om dit te doen, `AudioConfig` gewoon weglaten `SpeechSynthesizer` van de param bij het maken van de in het bovenstaande voorbeeld. Hiermee wordt naar het huidige actieve uitvoerapparaat uitgevoerd.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config);
    auto result = synthesizer->SpeakTextAsync("Synthesizing directly to speaker output.").get();
}
```

## <a name="get-result-as-an-in-memory-stream"></a>Resultaat krijgen als een in-memory stream

Voor veel scenario's in de ontwikkeling van spraaktoepassingen hebt u waarschijnlijk de resulterende audiogegevens nodig als een in-memory stream in plaats van rechtstreeks naar een bestand te schrijven. Hiermee u aangepast gedrag op te bouwen, waaronder:

* De resulterende bytearray abstraheren als een zoekbare stream voor aangepaste downstreamservices.
* Integreer het resultaat met andere API's of services.
* Wijzig de audiogegevens, `.wav` schrijf aangepaste kopteksten, enz.

Het is eenvoudig om deze wijziging aan te brengen ten opzichte van het vorige voorbeeld. Verwijder eerst `AudioConfig`de , als u het uitvoergedrag handmatig te beheren vanaf dit punt verder voor meer controle. Ga `NULL` dan `AudioConfig` voor `SpeechSynthesizer` de in de constructor. 

> [!NOTE]
> Het `NULL` doorgeven `AudioConfig`van de , in plaats van weglaten zoals in de luidspreker uitgang voorbeeld hierboven, zal niet standaard afspelen van de audio op de huidige actieve uitvoer apparaat.

Deze keer slaat u het [`SpeechSynthesisResult`](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesisresult) resultaat op een variabele. De `GetAudioData` getter `byte []` retourneert een van de uitvoergegevens. U hiermee `byte []` handmatig werken of u [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) de klasse gebruiken om de in-memory stream te beheren. In dit voorbeeld `AudioDataStream.FromResult()` gebruikt u de statische functie om een stroom uit het resultaat te halen.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    auto result = synthesizer->SpeakTextAsync("Getting the response as an in-memory stream.").get();
    auto stream = AudioDataStream::FromResult(result);
}
```

Vanaf hier u elk aangepast `stream` gedrag implementeren met behulp van het resulterende object.

## <a name="customize-audio-format"></a>Audio-indeling aanpassen

In de volgende sectie ziet u hoe u audio-uitvoerkenmerken aanpassen, waaronder:

* Audiobestandstype
* Steekproefsnelheid
* Bitdiepte

Als u de audio-indeling `SetSpeechSynthesisOutputFormat()` wilt `SpeechConfig` wijzigen, gebruikt u de functie op het object. Deze functie `enum` verwacht [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)een type, dat u gebruikt om de uitvoerindeling te selecteren. Zie de referentiedocumenten voor een [lijst met beschikbare audio-indelingen.](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat)

Er zijn verschillende opties voor verschillende bestandstypen, afhankelijk van uw vereisten. Houd er rekening mee `Raw24Khz16BitMonoPcm` dat raw-indelingen, zoals, per definitie geen audiokopteksten bevatten. Gebruik raw-indelingen alleen wanneer u weet dat uw downstream-implementatie een ruwe bitstream kan decoderen, of als u van plan bent om handmatig headers te bouwen op basis van bitdiepte, sample-rate, aantal kanalen, enz.

In dit voorbeeld geeft u een hifi-RIFF-indeling `Riff24Khz16BitMonoPcm` op door de `SpeechSynthesisOutputFormat` indeling op het object in te `SpeechConfig` stellen. Net als in het voorbeeld in [`AudioDataStream`](https://docs.microsoft.com/cpp/cognitive-services/speech/audiodatastream) de vorige sectie, gebruikt u om een in-memory stream van het resultaat te krijgen en vervolgens naar een bestand te schrijven.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config->SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat::Riff24Khz16BitMonoPcm);

    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    auto result = synthesizer->SpeakTextAsync("A simple test to write to a file.").get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
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

Vervolgens moet u het verzoek voor spraaksynthese wijzigen om naar uw XML-bestand te verwijzen. De aanvraag is meestal hetzelfde, `SpeakTextAsync()` maar in `SpeakSsmlAsync()`plaats van de functie te gebruiken, gebruikt u . Deze functie verwacht een XML-tekenreeks, zodat u eerst uw SSML-config als tekenreeks laadt. Vanaf hier is het resultaatobject precies hetzelfde als eerdere voorbeelden.

```cpp
void synthesizeSpeech() 
{
    auto config = SpeechConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    auto synthesizer = SpeechSynthesizer::FromConfig(config, NULL);
    
    std::ifstream file("./ssml.xml");
    std::string ssml, line;
    while (std::getline(file, line))
    {
        ssml += line;
        ssml.push_back('\n');
    }
    auto result = synthesizer->SpeakSsmlAsync(ssml).get();
    
    auto stream = AudioDataStream::FromResult(result);
    stream->SaveToWavFileAsync("path/to/write/file.wav").get();
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
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
