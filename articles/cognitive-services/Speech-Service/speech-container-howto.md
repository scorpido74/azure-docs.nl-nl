---
title: Spraak containers installeren-spraak service
titleSuffix: Azure Cognitive Services
description: Installeer en voer spraak containers uit. Met spraak naar tekst worden audio stromen naar tekst getranscribeerd in realtime die uw toepassingen, hulpprogram ma's of apparaten kunnen gebruiken of weer geven. Tekst-naar-spraak zet invoer tekst om in humane-achtige, gesynthesizerde spraak.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: dapine
ms.openlocfilehash: d5ecc104c7845a1881cbcdecfbccb75148f6e070
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815369"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Speech Service-containers installeren en uitvoeren (preview-versie)

Met containers kunt u enkele Api's van de speech-service uitvoeren in uw eigen omgeving. Containers zijn geweldig voor specifieke vereisten voor beveiliging en gegevens beheer. In dit artikel leert u hoe u een spraak container downloadt, installeert en uitvoert.

Met spraak containers kunnen klanten een spraak toepassings architectuur maken die is geoptimaliseerd voor zowel robuuste Cloud mogelijkheden als Edge-locatie. Er zijn vier verschillende containers beschikbaar. De twee standaard containers zijn **spraak-naar-tekst** en **tekst-naar-spraak**. De twee aangepaste containers zijn **Custom speech-naar-tekst** -en **aangepaste tekst-naar-spraak**.

> [!IMPORTANT]
> Alle spraak containers worden momenteel aangeboden als onderdeel van een [open bare preview-versie](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Er wordt een aankondiging gedaan wanneer de voortgang van de spraak containers op algemene Beschik baarheid (GA) wordt weer gegeven.

| Functie | Functies | Meest recent |
|--|--|--|
| Spraak naar tekst | Transcribeert doorlopend realtime spraak of batch opnames in tekst met tussenliggende resultaten. | 2.0.0 |
| Custom Speech-naar-tekst | Door gebruik te maken van een aangepast model van de [Custom speech Portal](https://speech.microsoft.com/customspeech), transcribeert doorlopend realtime spraak of batch opnames in tekst met tussenliggende resultaten. | 2.0.0 |
| Tekst naar spraak | Hiermee wordt tekst geconverteerd naar een spreek spraak met tekst zonder opmaak of een SSML (Speech synthese Markup Language). | 1.3.0 |
| Aangepaste tekst-naar-spraak | Door gebruik te maken van een aangepast model van de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal), converteert u tekst naar een natuurlijk geluids fragment met de invoer van een tekst zonder opmaak of een SSML (Speech synthese Markup Language). | 1.3.0 |

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten voordat u spraak containers gebruikt:

| Verplicht | Doel |
|--|--|
| Docker-engine | De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie voor een uitleg van de basisprincipes van Docker en containers, de [dockeroverzicht](https://docs.docker.com/engine/docker-overview/).<br><br> Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br> |
| Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker`-opdrachten. |
| Spraak resource | Als u deze containers wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure- _spraak_ bron om de bijbehorende API-sleutel en eind punt-URI op te halen. Beide waarden zijn beschikbaar op het **spraak** overzicht van de Azure Portal en op de pagina sleutels. Ze zijn beide nodig om de container te starten.<br><br>**{API_KEY}** : een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}** : het eind punt op de pagina **overzicht** |

## <a name="request-access-to-the-container-registry"></a>Toegang aanvragen tot het container register

Vul het [aanvraag formulier voor de Cognitive Services spraak containers](https://aka.ms/speechcontainerspreview/) in en verzend het om toegang tot de container aan te vragen. 

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

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke spraak container beschreven.

# <a name="speech-to-texttabstt"></a>[Spraak naar tekst](#tab/stt)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Spraak naar tekst | 2 Core, 2 GB geheugen | 4-core, 4 GB geheugen |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-naar-tekst](#tab/cstt)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Custom Speech-naar-tekst | 2 Core, 2 GB geheugen | 4-core, 4 GB geheugen |

# <a name="text-to-speechtabtts"></a>[Tekst naar spraak](#tab/tts)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Tekst naar spraak | 1 Core, 2 GB geheugen | 2 Core, 3 GB geheugen |

# <a name="custom-text-to-speechtabctts"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Aangepaste tekst-naar-spraak | 1 Core, 2 GB geheugen | 2 Core, 3 GB geheugen |

***

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Core en geheugen komen overeen met de instellingen `--cpus` en `--memory` die worden gebruikt als onderdeel van de `docker run` opdracht.

> [!NOTE]
> De minimale en aanbevolen waarde zijn gebaseerd op de limieten van docker, *niet* op de hostcomputer. Bijvoorbeeld: spraak naar tekst containers delen van een groot taal model en het wordt *Aanbevolen* dat het hele bestand in het geheugen past, wat een extra 4-6 GB is. Het is ook mogelijk dat de eerste uitvoering van een van de containers langer duurt, omdat modellen in het geheugen worden gewisseld.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie met `docker pull` ophalen

Container installatie kopieën voor spraak zijn beschikbaar in de volgende Container Registry.

# <a name="speech-to-texttabstt"></a>[Spraak naar tekst](#tab/stt)

| Container | Opslagplaats |
|-----------|------------|
| Spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-naar-tekst](#tab/cstt)

| Container | Opslagplaats |
|-----------|------------|
| Custom Speech-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Tekst naar spraak](#tab/tts)

| Container | Opslagplaats |
|-----------|------------|
| Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

| Container | Opslagplaats |
|-----------|------------|
| Aangepaste tekst-naar-spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-pull voor de spraak containers

# <a name="speech-to-texttabstt"></a>[Spraak naar tekst](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-pull voor de spraak-naar-tekst-container

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden uit het REGI ster van container voorbeelden.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> De `latest` tag trekt de `en-US` land instelling. Zie voor aanvullende land instellingen voor [spraak naar tekst](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Spraak-naar-tekst-land instellingen

Alle labels, met uitzonde ring van `latest`, hebben de volgende indeling en zijn hoofdletter gevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

De volgende code is een voor beeld van de indeling:

```
2.0.0-amd64-en-us-preview
```

Zie voor alle ondersteunde land instellingen van de **spraak-naar-tekst** -container Tags voor [spraak-naar-tekst-afbeelding](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-naar-tekst](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-pull voor de Custom Speech-naar-tekst-container

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden uit het REGI ster van container voorbeelden.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> De `locale` en `voice` voor aangepaste spraak containers worden bepaald door het aangepaste model dat door de container is opgenomen.

# <a name="text-to-speechtabtts"></a>[Tekst naar spraak](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-pull voor de tekst-naar-spraak-container

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden uit het REGI ster van container voorbeelden.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> De `latest` tag haalt de `en-US` land instellingen en `jessarus` stem op. Zie [land instellingen voor tekst naar spraak](#text-to-speech-locales)voor aanvullende land instellingen.

#### <a name="text-to-speech-locales"></a>Land instellingen voor tekst naar spraak

Alle labels, met uitzonde ring van `latest`, hebben de volgende indeling en zijn hoofdletter gevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

De volgende code is een voor beeld van de indeling:

```
1.3.0-amd64-en-us-jessarus-preview
```

Zie [tekst-naar-spraak-afbeeldings Tags](../containers/container-image-tags.md#text-to-speech)voor alle ondersteunde land instellingen en overeenkomstige stemmen van de **tekst-naar-spraak** -container.

> [!IMPORTANT]
> Bij het maken van een *standaard tekst-naar-spraak* -http post moet het [SSML-bericht (Speech synthese Markup Language)](speech-synthesis-markup.md) een `voice`-element met een `name` kenmerk hebben. De waarde is de overeenkomstige land instellingen voor containers en spraak, ook wel bekend als de [' short name '](language-support.md#standard-voices). Het `latest` label zou bijvoorbeeld een spraak naam van `en-US-JessaRUS`hebben.

# <a name="custom-text-to-speechtabctts"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

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

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](speech-container-configuration.md#example-docker-run-commands) van de `docker run`-opdracht beschikbaar.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-with-docker-run"></a>De container met `docker run` uitvoeren

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het ophalen van de `{Endpoint_URI}` en `{API_Key}` waarden. Er zijn ook aanvullende [voor beelden](speech-container-configuration.md#example-docker-run-commands) van de opdracht `docker run` beschikbaar.

# <a name="speech-to-texttabstt"></a>[Spraak naar tekst](#tab/stt)

Voer de `docker run` volgende opdracht uit om de *spraak-naar-tekst-* container uit te voeren.

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

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-naar-tekst](#tab/cstt)

De *Custom speech-naar-tekst* -container is afhankelijk van een aangepast spraak model. Het aangepaste model moet zijn [getraind](how-to-custom-speech-train-model.md) met behulp van de [aangepaste spraak Portal](https://speech.microsoft.com/customspeech). De ID van het aangepaste spraak **model** is vereist voor het uitvoeren van de container. U kunt dit vinden op de pagina **training** van de portal voor aangepaste spraak. Ga in de aangepaste spraak Portal naar de pagina **training** en selecteer het model.
<br>

![Pagina aangepaste spraak training](media/custom-speech/custom-speech-model-training.png)

Haal de **model-id** op die moet worden gebruikt als argument voor de para meter `ModelId` van de opdracht `docker run`.
<br>

![Details van het aangepaste spraak model](media/custom-speech/custom-speech-model-details.png)

De volgende tabel bevat de verschillende `docker run` para meters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | Het [volume](https://docs.docker.com/storage/volumes/)van de hostcomputer, dat door docker wordt gebruikt om het aangepaste model persistent te maken. Bijvoorbeeld *C:\CustomSpeech* waarbij *station C* zich op de hostcomputer bevindt. |
| `{MODEL_ID}` | De ID van het Custom Speech **model** op de pagina **training** van de portal voor aangepaste spraak. |
| `{ENDPOINT_URI}` | Het eind punt is vereist voor het meten en factureren. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |
| `{API_KEY}` | De API-sleutel is vereist. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |

Als u de *Custom speech-naar-tekst* -container wilt uitvoeren, voert u de volgende `docker run`-opdracht uit:

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
* Hiermee downloadt u het model op basis van de `ModelId` (indien niet gevonden op de volume koppeling).
* Als het aangepaste model eerder is gedownload, wordt de `ModelId` genegeerd.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

# <a name="text-to-speechtabtts"></a>[Tekst naar spraak](#tab/tts)

Voer de volgende `docker run` opdracht uit om de *tekst-naar-spraak-* container uit te voeren.

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

# <a name="custom-text-to-speechtabctts"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

De *aangepaste tekst-naar-spraak* -container is afhankelijk van een aangepast spraak model. Het aangepaste model moet zijn [getraind](how-to-custom-voice-create-voice.md) met behulp van de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal). De ID van het aangepaste spraak **model** is vereist voor het uitvoeren van de container. U kunt dit vinden op de pagina **training** van de aangepaste Voice Portal. Ga vanuit de aangepaste Voice Portal naar de pagina **training** en selecteer het model.
<br>

![Pagina aangepaste spraak training](media/custom-voice/custom-voice-model-training.png)

Haal de **model-id** op die moet worden gebruikt als argument voor de para meter `ModelId` van de opdracht docker run.
<br>

![Aangepaste Details van het spraak model](media/custom-voice/custom-voice-model-details.png)

De volgende tabel bevat de verschillende `docker run` para meters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | Het [volume](https://docs.docker.com/storage/volumes/)van de hostcomputer, dat door docker wordt gebruikt om het aangepaste model persistent te maken. Bijvoorbeeld *C:\CustomSpeech* waarbij *station C* zich op de hostcomputer bevindt. |
| `{MODEL_ID}` | De ID van het Custom Speech **model** op de pagina **training** van de aangepaste Voice Portal. |
| `{ENDPOINT_URI}` | Het eind punt is vereist voor het meten en factureren. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |
| `{API_KEY}` | De API-sleutel is vereist. Zie [vereiste para meters verzamelen](#gathering-required-parameters)voor meer informatie. |

Als u de *aangepaste tekst-naar-spraak-* container wilt uitvoeren, voert u de volgende `docker run`-opdracht uit:

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
* Hiermee downloadt u het model op basis van de `ModelId` (indien niet gevonden op de volume koppeling).
* Als het aangepaste model eerder is gedownload, wordt de `ModelId` genegeerd.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

***

> [!IMPORTANT]
> De `Eula`, `Billing`, en `ApiKey` opties moeten worden opgegeven voor het uitvoeren van de container; anders wordt de container niet start.  Zie voor meer informatie, [facturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

| Containers | SDK-host-URL | Protocol |
|--|--|--|
| Spraak naar tekst en Custom Speech-naar-tekst | `ws://localhost:5000` | WS |
| Tekst-naar-spraak en aangepaste tekst-naar-spraak | `http://localhost:5000` | HTTP |

Zie [Container Security](../cognitive-services-container-support.md#azure-cognitive-services-container-security)(Engelstalig) voor meer informatie over het gebruik van WSS-en HTTPS-protocollen.

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

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

Zie voor meer informatie over deze opties [containers configureren](speech-container-configuration.md).

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
* Containerinstallatiekopieën uitvoeren in Docker.
* U kunt de REST API of SDK gebruiken voor het aanroepen van bewerkingen in spraak containers door de URI van de host op te geven van de container.
* U moet facturerings gegevens opgeven bij het instantiëren van een container.

> [!IMPORTANT]
>  Cognitive Services-containers zijn geen licentie om uit te voeren zonder verbinding met Azure voor het meten. Klanten moeten de containers om te communiceren factureringsgegevens met de softwarelicentiecontrole-service te allen tijde inschakelen. Cognitive Services-containers verzenden klantgegevens (zoals de afbeelding of tekst die wordt geanalyseerd) niet naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Containers voor configuratie-instellingen [configureren](speech-container-configuration.md) controleren
* Meer informatie over het [gebruik van Speech Service-containers met Kubernetes en helm](speech-container-howto-on-premises.md)
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
