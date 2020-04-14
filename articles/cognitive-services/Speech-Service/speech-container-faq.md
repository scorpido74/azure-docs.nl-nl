---
title: Spraakservicecontainers veelgestelde vragen (FAQ)
titleSuffix: Azure Cognitive Services
description: Spraakcontainers installeren en uitvoeren. spraak-naar-tekst transcribeert audiostreams naar tekst in realtime die uw toepassingen, hulpprogramma's of apparaten kunnen gebruiken of weergeven. Tekst-naar-spraak zet invoertekst om in menselijke gesynthetiseerde spraak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 4d597b872cf31a823f314d9f3c67c9f45201c542
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258470"
---
# <a name="speech-service-containers-frequently-asked-questions-faq"></a>Spraakservicecontainers veelgestelde vragen (FAQ)

Vertrouw bij het gebruik van de Spraakservice met containers op deze verzameling veelgestelde vragen voordat deze escaleert om te ondersteunen. Dit artikel legt vragen in verschillende mate vast, van algemeen tot technisch. Als u een antwoord wilt uitbreiden, klikt u op de vraag.

## <a name="general-questions"></a>Algemene vragen

<details>
<summary>
<b>Hoe werken spraakcontainers en hoe stel ik ze in?</b>
</summary>

**Antwoord:** Bij het opzetten van het productiecluster zijn er verschillende dingen om rekening mee te houden. Ten eerste, het opzetten van enkele taal, meerdere containers, op dezelfde machine, mag niet een groot probleem. Als u problemen ondervindt, kan het een hardware-gerelateerd probleem zijn - dus we zouden eerst naar resource kijken, dat wil zeggen; CPU- en geheugenspecificaties.

Denk even na `ja-JP` over de container en het nieuwste model. Het akoestische model is het meest veeleisende stuk CPU-gewijs, terwijl het taalmodel het meeste geheugen vereist. Toen we het gebruik benchmarkten, duurt het ongeveer 0,6 CPU-cores om één spraak-naar-tekstverzoek te verwerken wanneer audio in realtime binnenstroomt (zoals vanuit de microfoon). Als u audio sneller voedt dan real-time (zoals in een bestand), kan dat gebruik verdubbelen (1,2x cores). Ondertussen is het geheugen hieronder is het werkgeheugen voor het decoderen van spraak. Er wordt *geen* rekening gehouden met de werkelijke volledige grootte van het taalmodel, dat zich in de bestandscache bevindt. Want `ja-JP` dat is een extra 2 GB; want, `en-US`het kan meer (6-7 GB).

Als u een machine hebt waar het geheugen schaars is en u probeert er meerdere talen op te implementeren, is het mogelijk dat de bestandscache vol is en het besturingssysteem wordt gedwongen om modellen in en uit te pagina's. Voor een lopende transcriptie, dat kan rampzalig zijn, en kan leiden tot vertragingen en andere gevolgen voor de prestaties.

Verder verpakken we executables voor machines met de [geavanceerde vectorextensie (AVX2)](speech-container-howto.md#advanced-vector-extension-support) instructieset. Een machine met de AVX512-instructieset vereist codegeneratie voor dat doel en het starten van 10 containers voor 10 talen kan de CPU tijdelijk uitputten. Een bericht als dit verschijnt in de dockerlogboeken:

```console
2020-01-16 16:46:54.981118943 
[W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache]
Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...
```

Ten slotte u het gewenste aantal *single* decoders `DECODER MAX_COUNT` in één container instellen met behulp van variabele. Dus, in principe, moeten we beginnen met uw SKU (CPU / geheugen), en we kunnen voorstellen hoe het beste uit te halen. Een goed uitgangspunt is te verwijzen naar de aanbevolen host machine resource specificaties.

<br>
</details>

<details>
<summary>
<b>u helpen met capaciteitsplanning en kostenschatting van on-prem Speech containers?</b>
</summary>

**Antwoord:** Voor containercapaciteit in batchverwerkingsmodus kon elke decoder 2-3x in realtime, met twee CPU-cores, verwerken voor één herkenning. We raden u aan om meer dan twee gelijktijdige herkenningen per containerinstantie te behouden, maar raden u aan meer exemplaren van containers uit te voeren om redenen van betrouwbaarheid/beschikbaarheid, achter een load balancer.

Hoewel we konden hebben elke container instantie draait met meer decoders. We kunnen bijvoorbeeld 7 decoders per containerexemplaar instellen op een achtkernmachine (op meer dan 2x per stuk), wat 15x doorvoer oplevert. Er is een `DECODER_MAX_COUNT` param bewust te zijn van. In het uiterste geval doen zich problemen met betrouwbaarheid en latentie voor, waarbij de doorvoer aanzienlijk is toegenomen. Voor een microfoon, zal het op 1x real-time. Het totale gebruik moet op ongeveer een kern voor een enkele erkenning.

Voor scenario van verwerking 1 K uur/ dag in batch processing mode, in een extreem geval, 3 VM's kon verwerken binnen 24 uur, maar niet gegarandeerd. Om piekdagen, failover, update en minimale back-up/BCP te verwerken, raden we 4-5 machines aan in plaats van 3 per cluster en met 2+ clusters.

Voor hardware gebruiken we `DS13_v2` standaard Azure VM als referentie (elke core moet 2,6 GHz of beter zijn, met AVX2-instructieset ingeschakeld).

| Exemplaar  | vCPU(s) | RAM    | Tijdelijke opslag | Pay-as-you-go met AHB | 1-jarige reserve bij AHB (% Besparingen) | 3 jaar gereserveerd bij AHB (% Besparingen) |
|-----------|---------|--------|--------------|------------------------|-------------------------------------|--------------------------------------|
| `DS13 v2` | 8       | 56 GiB | 112 GiB      | $0.598/uur            | $0,3528/uur (~41%)                 | $0,2333/uur (~61%)                  |

Op basis van de ontwerpreferentie (twee clusters van 5 VM's voor de verwerking van 1 K uur/dag audiobatch), zijn de hardwarekosten van 1 jaar:

> 2 (clusters) * 5 (VM's per cluster) * $0.3528/uur * 365 (dagen) * 24 (uren) = $31K / jaar

Bij het in kaart brengen naar de fysieke machine is een algemene schatting 1 vCPU = 1 Fysieke CPU-kern. In werkelijkheid is 1vCPU krachtiger dan een enkele kern.

Voor on-prem, al deze extra factoren in het spel komen:

- Op welk type de fysieke CPU is en hoeveel cores erop
- Hoeveel CPU's die samen op dezelfde doos/machine worden uitgevoerd
- Hoe VM's zijn ingesteld
- Hoe hyper-threading / multi-threading wordt gebruikt
- Hoe geheugen wordt gedeeld
- Het OS, enz.

Normaal gesproken is het niet zo goed afgestemd als Azure de omgeving. Gezien andere overhead, zou ik zeggen dat een veilige schatting is 10 fysieke CPU-cores = 8 Azure vCPU. Hoewel populaire CPU's slechts acht cores hebben. Met on-prem-implementatie zijn de kosten hoger dan het gebruik van Azure VM's. Houd ook rekening met het afschrijvingspercentage.

Servicekosten zijn hetzelfde als de online service: $ 1 / uur voor spraak-naar-tekst. De kosten voor spraakservice zijn:

> $1 * 1000 * 365 = $365K

Onderhoudskosten die aan Microsoft worden betaald, zijn afhankelijk van het serviceniveau en de inhoud van de service. Het verschillende van $ 29.99 / maand voor het basisniveau tot honderdduizenden als on-site service betrokken. Een ruw aantal is $300/uur voor service/onderhoud. Personenkosten zijn niet inbegrepen. Andere infrastructuurkosten (zoals opslag, netwerken en load balancers) zijn niet inbegrepen.

<br>
</details>

<details>
<summary>
<b>Waarom ontbreekt interpunctie in de transcriptie?</b>
</summary>

**Antwoord:** De `speech_recognition_language=<YOUR_LANGUAGE>` aanvraag moet expliciet worden geconfigureerd in de aanvraag als ze de Koolstofclient gebruiken.

Bijvoorbeeld:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Dit is de uitvoer:

```cmd
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生まれたかとんと見当を検討をなつかぬ。
    何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。
    まだは今ここで初めて人間と言うものを見た。
    しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

<br>
</details>

<details>
<summary>
<b>Kan ik een aangepast akoestisch model en taalmodel gebruiken met Spraakcontainer?</b>
</summary>

We zijn momenteel slechts in staat om een model-ID, hetzij aangepaste taal model of aangepaste akoestische model passeren.

**Antwoord:** De beslissing om *niet* te ondersteunen zowel akoestische en taalmodellen gelijktijdig werd gemaakt. Dit blijft van kracht totdat er een uniforme id wordt gemaakt om API-onderbrekingen te verminderen. Dus, helaas is dit niet ondersteund op dit moment.

<br>
</details>

<details>
<summary>
<b>u deze fouten uitleggen uit de aangepaste spraak-naar-tekst container?</b>
</summary>

**Fout 1:**

```cmd
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**Antwoord 1:** Als u traint met het nieuwste aangepaste model, ondersteunen we dat momenteel niet. Als u traint met een oudere versie, moet het mogelijk zijn om te gebruiken. We zijn nog steeds bezig met het ondersteunen van de nieuwste versies.

In wezen ondersteunen de aangepaste containers geen Halide- of ONNX-gebaseerde akoestische modellen (wat de standaard is in het aangepaste trainingsportaal). Dit is echter te wijten aan aangepaste modellen die niet worden versleuteld en we willen ONNX-modellen niet blootstellen; taalmodellen zijn prima. De klant moet expliciet een ouder niet-ONNX-model selecteren voor aangepaste training. Nauwkeurigheid wordt niet beïnvloed. De grootte van het model kan groter zijn (met 100 MB).

> Ondersteuningsmodel > 20190220 (v4.5 Unified)

**Fout 2:**

```cmd
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**Antwoord 2:** U moet de juiste stemnaam opgeven in het verzoek, dat hoofdlettergevoelig is. Raadpleeg de full service naamtoewijzing. U moet `en-US-JessaRUS`gebruiken, `en-US-JessaNeural` zoals niet beschikbaar is op dit moment in container versie van tekst-naar-spraak.

**Fout 3:**

```json
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**Antwoord 3:** U wilt een spraakbron maken, niet een resource voor Cognitive Services.


<br>
</details>

<details>
<summary>
<b>Welke API-protocollen worden ondersteund, REST of WS?</b>
</summary>

**Antwoord:** Voor spraak-naar-tekst- en aangepaste spraak-naar-tekstcontainers ondersteunen we momenteel alleen het websocket-protocol. De SDK ondersteunt alleen bellen in WS, maar niet REST. Er is een plan om REST-ondersteuning toe te voegen, maar niet ETA voor het moment. Altijd verwijzen naar de officiële documentatie, zie [query voorspelling eindpunten](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Wordt CentOS ondersteund voor Spraakcontainers?</b>
</summary>

**Antwoord:** CentOS 7 wordt nog niet ondersteund door Python SDK, ook Ubuntu 19.04 wordt niet ondersteund.

Het Python Speech-SDK-pakket is beschikbaar voor deze besturingssystemen:
- **Windows** - x64 en x86
- **Mac** - macOS X versie 10.12 of hoger
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 op x64

Zie [Python-platformsetup](quickstarts/setup-platform.md?pivots=programming-language-python)voor meer informatie over het instellen van de omgeving. Voor nu is Ubuntu 18.04 de aanbevolen versie.

<br>
</details>

<details>
<summary>
<b>Waarom krijg ik fouten wanneer ik LUIS-voorspellingseindpunten probeer aan te roepen?</b>
</summary>

Ik gebruik de LUIS-container in een IoT Edge-implementatie en probeer het LUIS-voorspellingseindpunt vanuit een andere container te bellen. De LUIS-container luistert op poort 5001 en de URL die ik gebruik is als volgt:

```csharp
var luisEndpoint =
    $"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict";
var config = SpeechConfig.FromEndpoint(new Uri(luisEndpoint));
```

De fout die ik krijg is:

```cmd
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
```

Ik zie het verzoek in de LUIS container logs en het bericht zegt:

```cmd
The request path /luis//predict" does not match a supported file type.
```

Wat betekent dit? Wat mis ik? Ik volgde het voorbeeld voor de Speech SDK, vanaf [hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Het scenario is dat we de audio rechtstreeks vanuit de pc-microfoon detecteren en proberen de intentie te bepalen, op basis van de LUIS-app die we hebben getraind. Het voorbeeld dat ik gekoppeld aan precies dat doet. En het werkt goed met de LUIS cloud-gebaseerde service. Met behulp van de Speech SDK leek ons te redden van het hebben van een aparte expliciete oproep aan de spraak-naar-tekst API en vervolgens een tweede oproep aan LUIS.

Dus, alles wat ik probeer te doen is overschakelen van het scenario van het gebruik van LUIS in de cloud naar het gebruik van de LUIS container. Ik kan me niet voorstellen als de Speech SDK werkt voor een, zal het niet werken voor de andere.

**Antwoord:** De SpraakSDK mag niet worden gebruikt tegen een LUIS-container. Voor het gebruik van de LUIS-container moet de LUIS SDK- of LUIS REST API worden gebruikt. Spraak SDK moet worden gebruikt tegen een spraakcontainer.

Een wolk is anders dan een container. Een cloud kan bestaan uit meerdere geaggregeerde containers (ook wel microservices genoemd). Dus er is een LUIS container en dan is er een Speech container - Twee aparte containers. De spraakcontainer doet alleen spraak. De LUIS-container doet alleen LUIS. In de cloud, omdat bekend is dat beide containers worden geïmplementeerd, en het slechte prestaties is voor een externe client om naar de cloud te gaan, spraak te doen, terug te komen, dan weer naar de cloud te gaan en LUIS te doen, bieden we een functie waarmee de client naar Spraak kan gaan, in de cloud blijft, naar LUIS gaat en vervolgens terugkomt naar de client. Dus zelfs in dit scenario gaat de Speech SDK naar Speech cloud container met audio, en vervolgens Speech cloud container praat met LUIS cloud container met tekst. De LUIS container heeft geen concept van het accepteren van audio (het zou niet zinvol zijn voor LUIS container te accepteren streaming audio - LUIS is een tekst-gebaseerde dienst). Met on-prem hebben we geen zekerheid dat onze klant beide containers heeft ingezet, we nemen niet aan om te orkestreren tussen containers in het pand van onze klanten, en als beide containers on-prem worden ingezet, gezien ze meer lokaal zijn voor de klant, is het geen last om eerst de SR te gaan, terug naar de klant, en laat de klant die tekst vervolgens nemen en naar LUIS gaan.

<br>
</details>

<details>
<summary>
<b>Waarom krijgen we fouten met macOS, Speech container en de Python SDK?</b>
</summary>

Wanneer we een *.wav-bestand* sturen dat moet worden getranscribeerd, komt het resultaat terug met:

```cmd
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket
{
    url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1",
    readyState: 0,
    bufferedAmount: 0,
    onopen: null,
    onerror: null,
    ...
}
```

We weten dat de websocket correct is ingesteld.

**Antwoord:** Als dat het geval is, zie dan [deze GitHub probleem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310). We hebben een work-around, [voorgesteld hier](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722).

Carbon heeft dit bevestigd bij versie 1.8.


<br>
</details>

<details>
<summary>
<b>Wat zijn de verschillen in de speech container eindpunten?</b>
</summary>

u helpen bij het invullen van de volgende teststatistieken, waaronder welke functies u moet testen en hoe u de SDK- en REST-API's testen? Vooral verschillen in "interactieve" en "conversatie", die ik niet zag uit de bestaande doc / sample.

| Eindpunt                                                | Functionele test                                                   | SDK | REST-API |
|---------------------------------------------------------|-------------------------------------------------------------------|-----|----------|
| `/speech/synthesize/cognitiveservices/v1`               | Tekst synthetiseren (tekst-naar-spraak)                                  |     | Ja      |
| `/speech/recognition/dictation/cognitiveservices/v1`    | Cognitive Services on-prem dicteerv1 websocket eindpunt        | Ja | Nee       |
| `/speech/recognition/interactive/cognitiveservices/v1`  | Het cognitive services on-prem interactief v1 websocket eindpunt  |     |          |
| `/speech/recognition/conversation/cognitiveservices/v1` | De cognitieve diensten on-prem gesprek v1 websocket eindpunt |     |          |

**Antwoord:** Dit is een fusie van:
- Mensen proberen het dicteereindpunt voor containers (ik weet niet zeker hoe ze die URL hebben gekregen)
- Het<sup>1e</sup> partijeindpunt is het eindpunt in een container.
- Het eindpunt voor het retourneren van spraak.fragment berichten van<sup>de</sup> eerste partij in plaats van de `speech.hypothesis` berichten die de eindpunten van het derde deel terugkeren voor het dicteereindpunt.<sup>rd</sup>
- De Carbon snelstart `RecognizeOnce` alle gebruik (interactieve modus)
- Carbon met een `speech.fragment` beweren dat voor berichten die vereisen dat ze niet worden geretourneerd in de interactieve modus.
- Carbon met de asserts brand in release bouwt (het doden van het proces).

De tijdelijke oplossing is overschakelen naar het gebruik van continue herkenning in uw code, of (sneller) verbinding maken met de interactieve of continue eindpunten in de container.
Stel voor uw code het eindpunt in op <host:port>/spraakherkenning/interactieve/cognitieve services/v1

Zie Spraakmodi voor de verschillende modi - zie hieronder:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

De juiste oplossing komt met SDK 1.8, die on-prem ondersteuning heeft (zal het juiste eindpunt te halen, dus we zullen niet slechter zijn dan online service). In de tussentijd is er een monster voor continue herkenning, waarom wijzen we er niet naar?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

<br>
</details>

<details>
<summary>
<b>Welke modus moet ik gebruiken voor verschillende audiobestanden?</b>
</summary>

**Antwoord:** Hier is een [snelle start met python.](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-python) U de andere talen vinden die zijn gekoppeld op de site met documenten.

Gewoon om te verduidelijken voor de interactieve, gesprek, en dictee; dit is een geavanceerde manier om de specifieke manier te specificeren waarop onze service het spraakverzoek zal behandelen. Helaas, voor de on-prem containers moeten we de volledige URI specificeren (omdat het lokale machine bevat), dus deze informatie lekte uit de abstractie. We werken samen met het SDK-team om dit in de toekomst bruikbaarder te maken.

<br>
</details>

<details>
<summary>
<b>Hoe kunnen we een ruwe meting van transacties/tweede/kern benchmarken?</b>
</summary>

**Antwoord:** Hier zijn enkele van de ruwe nummers te verwachten van bestaande model (zal veranderen ten goede in degene die we zullen het schip in GA):

- Voor bestanden, de beperking zal worden in de spraak SDK, op 2x. De eerste vijf seconden van de audio zijn niet beperkt. Decoder is in staat om te doen ongeveer 3x real-time. Hiervoor zal het totale CPU-gebruik dicht bij 2 cores zijn voor een enkele herkenning.
- Voor microfoon, zal het op 1x real-time. Het totale gebruik moet op ongeveer 1 kern voor een enkele erkenning.

Dit kan allemaal worden geverifieerd uit de docker logs. We dumpen de lijn met sessie- en woordgroep/utterancestatistieken, en dat geldt ook voor de RTF-nummers.


<br>
</details>

<details>
<summary>
<b>Is het gebruikelijk om audiobestanden te splitsen in chucks voor spraakcontainergebruik?</b>
</summary>

Mijn huidige plan is om een bestaand audiobestand te nemen en op te splitsen in 10 seconden brokken en stuur die door de container. Is dat een acceptabel scenario?  Is er een betere manier om grotere audiobestanden met de container te verwerken?

**Antwoord:** Gebruik gewoon de spraak SDK en geef het het bestand, zal het het juiste ding doen. Waarom moet je het bestand afdichten?


<br>
</details>

<details>
<summary>
<b>Hoe zorg ik ervoor dat meerdere containers op dezelfde host worden uitgevoerd?</b>
</summary>

De dokter zegt om een andere poort bloot te leggen, wat ik doe, maar de LUIS container luistert nog steeds op poort 5000?

**Antwoord:** Probeer `-p <outside_unique_port>:5000`. Bijvoorbeeld `-p 5001:5000`.


<br>
</details>

## <a name="technical-questions"></a>Technische vragen

<details>
<summary>
<b>Hoe kan ik niet-batch API's om audio &lt;te behandelen 15 seconden lang?</b>
</summary>

**Antwoord:** Dit is in interactieve modus. Als u dicteren of gesprek dat is geen probleem.


<br>
</details>

<details>
<summary>
<b>Wat zijn de aanbevolen bronnen, CPU en RAM; voor 50 gelijktijdige verzoeken?</b>
</summary>

Hoeveel gelijktijdige aanvragen zal een 4 core, 4 GB RAM handvat? Als we bijvoorbeeld 50 gelijktijdige aanvragen moeten serveren, hoeveel Cores en RAM worden aanbevolen?

**Antwoord:** Op real time, 8 `en-US`met onze nieuwste, dus we raden het gebruik van meer docker containers dan 6 gelijktijdige verzoeken. Het wordt gekker dan 16 cores, en het wordt niet-uniforme geheugentoegang (NUMA) knooppunt gevoelig. In de volgende tabel worden de minimale en aanbevolen toewijzing van resources voor elke spraakcontainer beschreven.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

| Container      | Minimum             | Aanbevolen         |
|----------------|---------------------|---------------------|
| Spraak naar tekst | 2-core geheugen met 2 GB | 4-core geheugen met 4 GB |

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

| Container             | Minimum             | Aanbevolen         |
|-----------------------|---------------------|---------------------|
| Aangepaste spraak-naar-tekst | 2-core geheugen met 2 GB | 4-core geheugen met 4 GB |

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

| Container      | Minimum             | Aanbevolen         |
|----------------|---------------------|---------------------|
| Tekst naar spraak | 1 core, 2 GB geheugen | 2-core geheugen met 3 GB |

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

| Container             | Minimum             | Aanbevolen         |
|-----------------------|---------------------|---------------------|
| Aangepaste tekst-naar-spraak | 1 core, 2 GB geheugen | 2-core geheugen met 3 GB |

***

- Elke kern moet minimaal 2,6 GHz of sneller zijn.
- Voor bestanden, de beperking zal worden in de Speech SDK, op 2x (de eerste 5 seconden van de audio zijn niet beperkt).
- De decoder is in staat om ongeveer 2-3x real-time te doen. Hiervoor zal het totale CPU-gebruik dicht bij twee cores zijn voor één herkenning. Daarom raden we aan om meer dan twee actieve verbindingen te houden, per containerexemplaar. De extreme kant zou zijn om ongeveer 10 decoders op 2x real-time in een acht core machine als `DS13_V2`. Voor de container versie 1.3 en later, er is `DECODER_MAX_COUNT=20`een param je zou kunnen proberen instellen .
- Voor microfoon, zal het op 1x real-time. Het totale gebruik moet op ongeveer een kern voor een enkele erkenning.

Houd rekening met het totale aantal uren audio dat u hebt. Als het aantal groot is, om de betrouwbaarheid/beschikbaarheid te verbeteren, raden we aan om meer exemplaren van containers uit te voeren, hetzij op één doos, hetzij op meerdere vakken, achter een load balancer. Orkestratie kan worden gedaan met Kubernetes (K8S) en Helm, of met Docker componeren.

Als voorbeeld, om 1000 uur/24 uur te verwerken, hebben we geprobeerd 3-4 VM's in te stellen, met 10 exemplaren/decoders per VM.

<br>
</details>

<details>
<summary>
<b>Ondersteunt de Speech container interpunctie?</b>
</summary>

**Antwoord:** We hebben kapitalisatie (ITN) beschikbaar in de on-prem container. Interpunctie is taalafhankelijk en wordt niet ondersteund voor sommige talen, waaronder Chinees en Japans.

We hebben *impliciete* en elementaire interpunctieondersteuning voor de `off` bestaande containers, maar het is standaard. Wat dat betekent is dat `.` je het personage in `。` je voorbeeld krijgen, maar niet het personage. Om deze impliciete logica in te schakelen, volgt hier een voorbeeld van hoe u dit doen in Python met behulp van onze Speech SDK (het zou vergelijkbaar zijn in andere talen):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

<br>
</details>

<details>
<summary>
<b>Waarom krijg ik 404 fouten wanneer ik gegevens naar spraak-naar-tekstcontainer probeer te posten?</b>
</summary>

Hier is een voorbeeld HTTP POST:

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**Antwoord:** We ondersteunen geen REST API in een van beide spraak-naar-tekst container, we ondersteunen alleen WebSockets via de Speech SDK. Altijd verwijzen naar de officiële documentatie, zie [query voorspelling eindpunten](speech-container-howto.md#query-the-containers-prediction-endpoint).

<br>
</details>

<details>
<summary>
<b>Waarom krijg ik deze fout wanneer u de spraak-naar-tekstservice gebruikt?</b>
</summary>

```cmd
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**Antwoord:** Dit gebeurt meestal wanneer u de audio sneller voedt dan de tekstvan spraakherkenningscontainer het kan gebruiken. Clientbuffers vullen zich en de annulering wordt geactiveerd. U moet de gelijktijdigheid en de RTF controleren waarop u de audio verzendt.

<br>
</details>

<details>
<summary>
<b>u deze tekst-naar-spraak container fouten uit de C ++ voorbeelden?</b>
</summary>

**Antwoord:** Als de containerversie ouder is dan 1.3, moet deze code worden gebruikt:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Oudere containers hebben niet het vereiste eindpunt voor `FromHost` Carbon om met de API te werken. Als de containers die voor versie 1.3 worden gebruikt, deze code moeten worden gebruikt:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Hieronder vindt u een `FromEndpoint` voorbeeld van het gebruik van de API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName(
    "Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 De `SetSpeechSynthesisVoiceName` functie wordt aangeroepen omdat de containers met een bijgewerkte tekst-naar-spraak-engine de spraaknaam vereisen.

<br>
</details>

<details>
<summary>
<b>Hoe kan ik v1.7 van de Speech SDK gebruiken met een Spraakcontainer?</b>
</summary>

**Antwoord:** Er zijn drie eindpunten op de spraakcontainer voor verschillende toepassingen, ze worden gedefinieerd als spraakmodi - zie hieronder:

[!INCLUDE [speech-modes](includes/speech-modes.md)]

Zij zijn voor verschillende doeleinden en worden verschillend gebruikt.

[Python-monsters](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py):
- Voor één herkenning (interactieve modus) met een aangepast eindpunt (dat wil zeggen; `SpeechConfig` met een parameter eindpunt), zie `speech_recognize_once_from_file_with_custom_endpoint_parameters()`.
- Zie voor `speech_recognize_continuous_from_file()`continue herkenning (gespreksmodus) en wijzig om een aangepast eindpunt als hierboven te gebruiken.
- Om dicteren in monsters zoals hierboven in te schakelen (alleen `speech_config`als je `speech_config.enable_dictation()`het echt nodig hebt), voeg direct nadat je hebt gemaakt, code toe.

Roep in C# dicteren in `SpeechConfig.EnableDictation()` om de functie in te schakelen.

### <a name="fromendpoint-apis"></a>`FromEndpoint`Apis
| Taal | API-details |
|----------|:------------|
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromendpoint" target="_blank">`SpeechConfig::FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet" target="_blank">`SpeechConfig.FromEndpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable" target="_blank">`SpeechConfig.fromendpoint` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithendpoint" target="_blank">`SPXSpeechConfiguration:initWithEndpoint;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Javascript | Momenteel niet ondersteund, noch is het gepland. |

<br>
</details>

<details>
<summary>
<b>Hoe kan ik v1.8 van de Speech SDK gebruiken met een Spraakcontainer?</b>
</summary>

**Antwoord:** Er is een `FromHost` nieuwe API. Dit vervangt of wijzigt geen bestaande API's. Het voegt enkel een alternatieve manier toe om een toespraakconfig te creëren gebruikend een douanegastheer.

### <a name="fromhost-apis"></a>`FromHost`Apis

| Taal | API-details |
|--|:-|
| C# | <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet" target="_blank">`SpeechConfig.FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| C++ | <a href="https://docs.microsoft.com/en-us/cpp/cognitive-services/speech/speechconfig#fromhost" target="_blank">`SpeechConfig::FromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Java | <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable" target="_blank">`SpeechConfig.fromHost` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Objective-C | <a href="https://docs.microsoft.com/en-us/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithhost" target="_blank">`SPXSpeechConfiguration:initWithHost;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Python | <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python" target="_blank">`SpeechConfig;` <span class="docon docon-navigate-external x-hidden-focus"></span></a> |
| Javascript | Momenteel niet ondersteund |

> Parameters: host (verplicht), abonnementssleutel (optioneel, als u de service zonder gebruiken).

Indeling voor `protocol://hostname:port` host `:port` is waar is optioneel (zie hieronder):
- Als de container lokaal wordt uitgevoerd, `localhost`is de hostnaam .
- Als de container op een externe server wordt uitgevoerd, gebruikt u de hostnaam of het IPv4-adres van die server.

Voorbeelden van hostparameters voor spraak-naar-tekst:
- `ws://localhost:5000`- niet-beveiligde verbinding met een lokale container via poort 5000
- `ws://some.host.com:5000`- niet-beveiligde verbinding met een container die op een externe server wordt uitgevoerd

Python monsters van bovenaf, maar gebruik `host` parameter in plaats van: `endpoint`

```python
speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")
```

<br>
</details>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Cognitive Services containers](speech-container-howto.md)
