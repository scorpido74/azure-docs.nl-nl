---
title: Spraak containers installeren-spraak service
titleSuffix: Azure Cognitive Services
description: Installeer en voer spraak containers uit. Met spraak naar tekst worden audio stromen naar tekst getranscribeerd in realtime die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Tekst-naar-spraak zet invoer tekst om in humane-achtige, gesynthesizerde spraak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.openlocfilehash: d1df46a5a8de4396011cc18ea062fe2d6b52ca8d
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773972"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Speech Service-containers installeren en uitvoeren (preview-versie)

Met containers kunt u enkele Api's van de speech-service uitvoeren in uw eigen omgeving. Containers zijn geweldig voor specifieke vereisten voor beveiliging en gegevens beheer. In dit artikel leert u hoe u een spraak container downloadt, installeert en uitvoert.

Met spraak containers kunnen klanten een spraak toepassings architectuur maken die is geoptimaliseerd voor zowel robuuste Cloud mogelijkheden als Edge-locatie. Er zijn vier verschillende containers beschikbaar. De twee standaard containers zijn **spraak-naar-tekst** en **tekst-naar-spraak**. De twee aangepaste containers zijn **Custom speech-naar-tekst** -en **aangepaste tekst-naar-spraak**. Spraak containers hebben dezelfde [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) als de op de cloud gebaseerde Azure speech Services.

> [!IMPORTANT]
> Alle spraak containers worden momenteel aangeboden als onderdeel van een [open bare preview-versie](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Er wordt een aankondiging gedaan wanneer de voortgang van de spraak containers op algemene Beschik baarheid (GA) wordt weer gegeven.

| Functie | Functies | Jongste |
|--|--|--|
| Spraak naar tekst | Analyseert sentiment en transcribeert doorlopend realtime spraak of batch audio-opnamen met tussenliggende resultaten.  | 2.2.0 |
| Custom Speech-naar-tekst | Door gebruik te maken van een aangepast model van de [Custom speech Portal](https://speech.microsoft.com/customspeech), transcribeert doorlopend realtime spraak of batch opnames in tekst met tussenliggende resultaten. | 2.2.0 |
| Tekst naar spraak | Hiermee wordt tekst geconverteerd naar een spreek spraak met tekst zonder opmaak of een SSML (Speech synthese Markup Language). | 1.4.0 |
| Aangepaste tekst-naar-spraak | Door gebruik te maken van een aangepast model van de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal), converteert u tekst naar een natuurlijk geluids fragment met de invoer van een tekst zonder opmaak of een SSML (Speech synthese Markup Language). | 1.4.0 |

Als u nog geen abonnement voor Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten voordat u spraak containers gebruikt:

| Vereist | Doel |
|--|--|
| Docker-engine | De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en facturerings gegevens kunnen verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br> |
| Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten. |
| Spraak resource | Als u deze containers wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure- _spraak_ bron om de bijbehorende API-sleutel en eind punt-URI op te halen. Beide waarden zijn beschikbaar op het **spraak** overzicht van de Azure Portal en op de pagina sleutels. Ze zijn beide nodig om de container te starten.<br><br>**{API_KEY}**: een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}**: het eind punt op de pagina **overzicht** |

## <a name="request-access-to-the-container-registry"></a>Toegang aanvragen tot het container register

Vul het [aanvraag formulier](https://aka.ms/speechcontainerspreview/) in en verzend het om toegang tot de container aan te vragen. 


[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Ondersteuning voor geavanceerde vector uitbreidingen

De **host** is de computer waarop de docker-container wordt uitgevoerd. De host *moet ondersteuning bieden* voor [Geavanceerde vector uitbreidingen](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). U kunt de AVX2-ondersteuning op Linux-hosts controleren met de volgende opdracht:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> De hostcomputer is *vereist* voor de ondersteuning van AVX2. De container werkt *niet* correct zonder ondersteuning voor AVX2.

### <a name="container-requirements-and-recommendations"></a>Container vereisten en aanbevelingen

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke spraak container beschreven.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Spraak naar tekst | 2 Core, 2 GB geheugen | 4-core, 4 GB geheugen |

# <a name="custom-speech-to-text"></a>[Custom Speech-naar-tekst](#tab/cstt)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Custom Speech-naar-tekst | 2 Core, 2 GB geheugen | 4-core, 4 GB geheugen |

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Tekst naar spraak | 1 Core, 2 GB geheugen | 2 Core, 3 GB geheugen |

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Aangepaste tekst-naar-spraak | 1 Core, 2 GB geheugen | 2 Core, 3 GB geheugen |

***

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van de `docker run` opdracht.

> [!NOTE]
> De minimale en aanbevolen waarde zijn gebaseerd op de limieten van docker, *niet* op de hostcomputer. Bijvoorbeeld: spraak naar tekst containers delen van een groot taal model en het wordt *Aanbevolen* dat het hele bestand in het geheugen past, wat een extra 4-6 GB is. Het is ook mogelijk dat de eerste uitvoering van een van de containers langer duurt, omdat modellen in het geheugen worden gewisseld.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met`docker pull`

Container installatie kopieën voor spraak zijn beschikbaar in de volgende Container Registry.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

| Container | Opslagplaats |
|-----------|------------|
| Spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Custom Speech-naar-tekst](#tab/cstt)

| Container | Opslagplaats |
|-----------|------------|
| Custom Speech-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

| Container | Opslagplaats |
|-----------|------------|
| Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

| Container | Opslagplaats |
|-----------|------------|
| Aangepaste tekst-naar-spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-pull voor de spraak containers

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-pull voor de spraak-naar-tekst-container

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden uit het REGI ster van container voorbeelden.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> De `latest` tag haalt de `en-US` land instelling op. Zie voor aanvullende land instellingen voor [spraak naar tekst](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Spraak-naar-tekst-land instellingen

Alle tags, met uitzonde ring van voor, `latest` hebben de volgende indeling en zijn hoofdletter gevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

De volgende code is een voor beeld van de indeling:

```
2.2.0-amd64-en-us-preview
```

Zie voor alle ondersteunde land instellingen van de **spraak-naar-tekst** -container Tags voor [spraak-naar-tekst-afbeelding](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Custom Speech-naar-tekst](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-pull voor de Custom Speech-naar-tekst-container

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden uit het REGI ster van container voorbeelden.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> De `locale` en `voice` voor aangepaste spraak containers worden bepaald door het aangepaste model dat door de container is opgenomen.

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-pull voor de tekst-naar-spraak-container

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden uit het REGI ster van container voorbeelden.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> De `latest` tag haalt de `en-US` land instellingen en de `jessarus` Stem op. Zie [land instellingen voor tekst naar spraak](#text-to-speech-locales)voor aanvullende land instellingen.

#### <a name="text-to-speech-locales"></a>Land instellingen voor tekst naar spraak

Alle tags, met uitzonde ring van voor, `latest` hebben de volgende indeling en zijn hoofdletter gevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

De volgende code is een voor beeld van de indeling:

```
1.3.0-amd64-en-us-jessarus-preview
```

Zie [tekst-naar-spraak-afbeeldings Tags](../containers/container-image-tags.md#text-to-speech)voor alle ondersteunde land instellingen en overeenkomstige stemmen van de **tekst-naar-spraak** -container.

> [!IMPORTANT]
> Bij het maken van een *standaard tekst-naar-spraak* -http post moet het [SSML-bericht (Speech synthese Markup Language)](speech-synthesis-markup.md) een `voice` element met een `name` kenmerk hebben. De waarde is de overeenkomstige land instellingen voor containers en spraak, ook wel bekend als de [' short name '](language-support.md#standard-voices). Het `latest` label zou bijvoorbeeld een spraak naam van hebben `en-US-JessaRUS` .

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-pull voor de aangepaste tekst-naar-spraak-container

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden uit het REGI ster van container voorbeelden.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> De `locale` en `voice` voor aangepaste spraak containers worden bepaald door het aangepaste model dat door de container is opgenomen.

***

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](speech-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het ophalen van de `{Endpoint_URI}` `{API_Key}` waarden en. Er zijn ook aanvullende [voor beelden](speech-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

Voer de volgende opdracht uit om de *spraak-naar-tekst* -container uit te voeren `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *spraak-naar-tekst* -container uit vanuit de container installatie kopie.
* Wijst 4 CPU-kernen en 4 GB aan geheugen toe.
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY voor de container toe.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Sentiment analyseren voor de uitvoer van spraak naar tekst 

Vanaf v 2.2.0 van de spraak-naar-tekst-container, kunt u de [API voor sentiment Analysis v3](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) op de uitvoer aanroepen. Voor het aanroepen van sentiment-analyse hebt u een Text Analytics-API resource-eind punt nodig. Bijvoorbeeld: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Als u toegang hebt tot een tekst analyse-eind punt in de Cloud, hebt u een sleutel nodig. Als u Text Analytics lokaal uitvoert, hoeft u dit mogelijk niet op te geven.

De sleutel en het eind punt worden door gegeven aan de spraak container als argumenten, zoals in het volgende voor beeld.

```bash
docker run -it --rm -p 5000:5000 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Met deze opdracht gebeurt het volgende:

* Voert dezelfde stappen uit als hierboven.
* Slaat een Text Analytics-API-eind punt en-sleutel op voor het verzenden van sentiment-analyse aanvragen. 


# <a name="custom-speech-to-text"></a>[Custom Speech-naar-tekst](#tab/cstt)

De *Custom speech-naar-tekst* -container is afhankelijk van een aangepast spraak model. Het aangepaste model moet zijn [getraind](how-to-custom-speech-train-model.md) met behulp van de [aangepaste spraak Portal](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Het Custom Speech model moet worden getraind vanuit een van de volgende model versies:
> * **20181201 (v 3.3 Unified)**
> * **20190520 (v 4.14 Unified)**
> * **20190701 (v 4.17 Unified)**<br>
> ![Custom Speech Train-container model](media/custom-speech/custom-speech-train-model-container-scoped.png)

De ID van het aangepaste spraak **model** is vereist voor het uitvoeren van de container. U kunt dit vinden op de pagina **training** van de portal voor aangepaste spraak. Ga in de aangepaste spraak Portal naar de pagina **training** en selecteer het model.
<br>

![Pagina aangepaste spraak training](media/custom-speech/custom-speech-model-training.png)

Haal de **model-id** op die moet worden gebruikt als argument voor de `ModelId` para meter van de `docker run` opdracht.
<br>

![Details van het aangepaste spraak model](media/custom-speech/custom-speech-model-details.png)

De volgende tabel bevat de verschillende `docker run` para meters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | Het [volume](https://docs.docker.com/storage/volumes/)van de hostcomputer, dat door docker wordt gebruikt om het aangepaste model persistent te maken. Bijvoorbeeld *C:\CustomSpeech* waarbij *station C* zich op de hostcomputer bevindt. |
| `{MODEL_ID}` | De ID van het Custom Speech **model** op de pagina **training** van de portal voor aangepaste spraak. |
| `{ENDPOINT_URI}` | Het eind punt is vereist voor het meten en factureren. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |
| `{API_KEY}` | De API-sleutel is vereist. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |

Als u de *Custom speech-naar-tekst* -container wilt uitvoeren, voert u de volgende `docker run` opdracht uit:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Hiermee wordt een *Custom speech-naar-tekst* -container uitgevoerd vanuit de container installatie kopie.
* Wijst 4 CPU-kernen en 4 GB aan geheugen toe.
* Hiermee wordt het *Custom speech-naar-tekst-* model geladen vanuit de volume-invoer koppeling, bijvoorbeeld *C:\CustomSpeech*.
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY voor de container toe.
* Hiermee downloadt u het model `ModelId` op basis van (indien niet gevonden op het volume koppel).
* Als het aangepaste model eerder is gedownload, `ModelId` wordt de genegeerd.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

Voer de volgende opdracht uit om de *tekst-naar-spraak-* container uit te voeren `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *tekst-naar-spraak* -container uit vanuit de container installatie kopie.
* Wijst twee CPU-kernen en een gigabyte (GB) aan geheugen toe.
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY voor de container toe.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

De *aangepaste tekst-naar-spraak* -container is afhankelijk van een aangepast spraak model. Het aangepaste model moet zijn [getraind](how-to-custom-voice-create-voice.md) met behulp van de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal). De ID van het aangepaste spraak **model** is vereist voor het uitvoeren van de container. U kunt dit vinden op de pagina **training** van de aangepaste Voice Portal. Ga vanuit de aangepaste Voice Portal naar de pagina **training** en selecteer het model.
<br>

![Pagina aangepaste spraak training](media/custom-voice/custom-voice-model-training.png)

Haal de **model-id** op die moet worden gebruikt als argument voor de `ModelId` para meter van de opdracht docker run.
<br>

![Aangepaste Details van het spraak model](media/custom-voice/custom-voice-model-details.png)

De volgende tabel bevat de verschillende `docker run` para meters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | Het [volume](https://docs.docker.com/storage/volumes/)van de hostcomputer, dat door docker wordt gebruikt om het aangepaste model persistent te maken. Bijvoorbeeld *C:\CustomSpeech* waarbij *station C* zich op de hostcomputer bevindt. |
| `{MODEL_ID}` | De ID van het Custom Speech **model** op de pagina **training** van de aangepaste Voice Portal. |
| `{ENDPOINT_URI}` | Het eind punt is vereist voor het meten en factureren. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |
| `{API_KEY}` | De API-sleutel is vereist. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |

Als u de *aangepaste tekst-naar-spraak-* container wilt uitvoeren, voert u de volgende `docker run` opdracht uit:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Hiermee wordt een *aangepaste tekst-naar-spraak-* container uitgevoerd vanuit de container installatie kopie.
* Wijst twee CPU-kernen en een gigabyte (GB) aan geheugen toe.
* Hiermee wordt het *aangepaste tekst-naar-spraak* -model geladen vanuit de volume-invoer koppeling, bijvoorbeeld *C:\CustomVoice*.
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY voor de container toe.
* Hiermee downloadt u het model `ModelId` op basis van (indien niet gevonden op het volume koppel).
* Als het aangepaste model eerder is gedownload, `ModelId` wordt de genegeerd.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

***

> [!IMPORTANT]
> De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](#billing)voor meer informatie.

## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

> [!NOTE]
> Gebruik een uniek poort nummer als u meerdere containers uitvoert.

| Containers | SDK-host-URL | Protocol |
|--|--|--|
| Spraak naar tekst en Custom Speech-naar-tekst | `ws://localhost:5000` | WS |
| Tekst-naar-spraak en aangepaste tekst-naar-spraak | `http://localhost:5000` | HTTP |

Zie [Container Security](../cognitive-services-container-support.md#azure-cognitive-services-container-security)(Engelstalig) voor meer informatie over het gebruik van WSS-en HTTPS-protocollen.

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Stemming analyseren

Als u uw Text Analytics-API referenties [aan de container](#analyze-sentiment-on-the-speech-to-text-output)hebt gegeven, kunt u de Speech SDK gebruiken voor het verzenden van aanvragen voor spraak herkenning met sentiment analyse. U kunt de API-antwoorden zo configureren dat ze een *eenvoudige* of *gedetailleerde* indeling hebben.

# <a name="simple-format"></a>[Eenvoudige indeling](#tab/simple-format)

Als u de speech-client wilt configureren voor gebruik van een eenvoudige indeling, voegt u `"Sentiment"` als waarde voor toe `Simple.Extensions` . Als u een specifieke versie van Text Analytics model wilt kiezen, vervangt u `'latest'` in de configuratie van de `speechcontext-phraseDetection.sentimentAnalysis.modelversion` eigenschap.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions`retourneert het sentiment-resultaat in de hoofdlaag van het antwoord.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Gedetailleerde indeling](#tab/detailed-format)

De speech-client configureren voor het gebruik van een gedetailleerde indeling, toevoegen `"Sentiment"` als waarde voor `Detailed.Extensions` , `Detailed.Options` of beide. Als u een specifieke versie van Text Analytics model wilt kiezen, vervangt u `'latest'` in de configuratie van de `speechcontext-phraseDetection.sentimentAnalysis.modelversion` eigenschap.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions`biedt sentiment-resultaat in de hoofdlaag van het antwoord. `Detailed.Options`Hiermee geeft u het resultaat in `NBest` de laag van het antwoord. Ze kunnen afzonderlijk of samen worden gebruikt.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Als u de sentiment-analyse volledig wilt uitschakelen, voegt u een `false` waarde toe aan `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-or-custom-text-to-speech"></a>Tekst-naar-spraak of aangepaste tekst-naar-spraak

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Meerdere containers op dezelfde host uitvoeren

Als u van plan bent om meerdere containers met blootgestelde poorten uit te voeren, moet u ervoor zorgen dat elke container wordt uitgevoerd met een andere beschik bare poort. Voer bijvoorbeeld de eerste container uit op poort 5000 en de tweede container op poort 5001.

U kunt deze container en een andere Azure Cognitive Services-container die samen op de HOST wordt uitgevoerd. U kunt ook meerdere containers van hetzelfde Cognitive Services-container uitvoeren.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer u de container start of uitvoert, kunnen er problemen optreden. Gebruik een uitvoer [koppeling](speech-container-configuration.md#mount-settings) en schakel logboek registratie in. Als u dit doet, kan de container logboek bestanden genereren die handig zijn bij het oplossen van problemen.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De spraak containers verzenden facturerings gegevens naar Azure met behulp van een *spraak* bron op uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [containers configureren](speech-container-configuration.md)voor meer informatie over deze opties.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd om spraak containers te downloaden, te installeren en uit te voeren. Samenvatting:

* Spraak biedt vier Linux-containers voor docker, die verschillende mogelijkheden inkapselen:
  * *Spraak naar tekst*
  * *Custom Speech-naar-tekst*
  * *Tekst naar spraak*
  * *Aangepaste tekst-naar-spraak*
* Container installatie kopieën worden gedownload uit het container register in Azure.
* Container installatie kopieën worden uitgevoerd in docker.
* Of u de REST API (alleen tekst naar spraak) of de SDK (spraak naar tekst of tekst naar spraak) wilt gebruiken, geeft u de URI van de host op. 
* U moet facturerings gegevens opgeven bij het instantiëren van een container.

> [!IMPORTANT]
>  Cognitive Services-containers mogen niet worden uitgevoerd zonder te zijn verbonden met Azure voor meting. Klanten moeten de containers in staat stellen om de facturerings gegevens te allen tijde met de meet service te communiceren. Cognitive Services containers verzenden geen klant gegevens (zoals de afbeelding of tekst die wordt geanalyseerd) naar micro soft.

## <a name="next-steps"></a>Volgende stappen

* Containers voor configuratie-instellingen [configureren](speech-container-configuration.md) controleren
* Meer informatie over het [gebruik van Speech Service-containers met Kubernetes en helm](speech-container-howto-on-premises.md)
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
