---
title: Spraakcontainers installeren - Spraakservice
titleSuffix: Azure Cognitive Services
description: Spraakcontainers installeren en uitvoeren. Spraak-naar-tekst transcribeert audiostreams naar tekst in realtime die uw toepassingen, hulpprogramma's of apparaten kunnen gebruiken of weergeven. Tekst-naar-spraak zet invoertekst om in menselijke gesynthetiseerde spraak.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2caae4fecdf13a1833f23cf9423cf3ded67f6f72
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879008"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Spraakservicecontainers installeren en uitvoeren (voorbeeld)

Met containers u een deel van de API's van de Spraakservice in uw eigen omgeving uitvoeren. Containers zijn ideaal voor specifieke vereisten op het gebied van beveiliging en gegevensbeheer. In dit artikel leert u hoe u een spraakcontainer downloaden, installeren en uitvoeren.

Spraakcontainers stellen klanten in staat om een spraaktoepassingsarchitectuur te bouwen die is geoptimaliseerd voor zowel robuuste cloudmogelijkheden als randplaats. Er zijn vier verschillende containers beschikbaar. De twee standaardcontainers zijn **Spraak-naar-tekst** **en Tekst-naar-spraak.** De twee aangepaste containers zijn **Aangepaste spraak-naar-tekst** en **aangepaste tekst-naar-spraak**.

> [!IMPORTANT]
> Alle spraakcontainers worden momenteel aangeboden als onderdeel van een [openbare "Gated" Preview.](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio) Er wordt een aankondiging gedaan wanneer spraakcontainers worden uitgevoerd naar Algemene Beschikbaarheid (GA).

| Functie | Functies | Laatste |
|--|--|--|
| Spraak naar tekst | Transcribeert continue real-time spraak- of batchaudio-opnamen in tekst met tussenliggende resultaten. | 2.1.1 |
| Aangepaste spraak-naar-tekst | Met behulp van een aangepast model van de [Custom Speech-portal](https://speech.microsoft.com/customspeech)transcribeert u continue real-time spraak- of batchaudio-opnamen in tekst met tussenliggende resultaten. | 2.1.1 |
| Tekst naar spraak | Converteert tekst naar natuurlijk klinkende spraak met platte tekstinvoer of Spraaksynthese-opmaaktaal (SSML). | 1.3.0 |
| Aangepaste tekst-naar-spraak | Met behulp van een aangepast model van de [Aangepaste Voice-portal](https://aka.ms/custom-voice-portal)wordt tekst omgezet in natuurlijk klinkende spraak met platte tekstinvoer of SSML (Speech Synthesis Markup Language). | 1.3.0 |

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten voordat u spraakcontainers gebruikt:

| Vereist | Doel |
|--|--|
| Docker-engine | U moet de Docker Engine geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden. <br><br> **Op Windows**moet Docker ook zijn geconfigureerd om Linux-containers te ondersteunen.<br><br> |
| Vertrouwdheid met Docker | U moet een basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers en `docker` containerafbeeldingen, evenals kennis van basisopdrachten. |
| Spraakbron | Om deze containers te kunnen gebruiken, moet u beschikken over:<br><br>Een Azure _Speech-bron_ om de bijbehorende API-sleutel en eindpunt URI op te halen. Beide waarden zijn beschikbaar op de **pagina's Spraakoverzicht** en Sleutels van de Azure-portal. Ze zijn beide verplicht om de container te starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de pagina **Sleutels**<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals vermeld op de **pagina Overzicht** |

## <a name="request-access-to-the-container-registry"></a>Toegang tot het containerregister aanvragen

Vul het [formulier Voor het aanvragen van het aanvraagformulier voor cognitive services-spraakcontainers](https://aka.ms/speechcontainerspreview/) in om toegang tot de container aan te vragen. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Ondersteuning voor geavanceerde vectorextensie

De **host** is de computer die de dockercontainer uitvoert. De host moet [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) *ondersteunen.* U met de volgende opdracht controleren op AVX2-ondersteuning op Linux-hosts:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> De hostcomputer is *vereist* om AVX2 te ondersteunen. De container *werkt niet* goed zonder AVX2-ondersteuning.

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

In de volgende tabel worden de minimale en aanbevolen toewijzing van resources voor elke spraakcontainer beschreven.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Spraak naar tekst | 2-core geheugen met 2 GB | 4-core geheugen met 4 GB |

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Aangepaste spraak-naar-tekst | 2-core geheugen met 2 GB | 4-core geheugen met 4 GB |

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Tekst naar spraak | 1 core, 2 GB geheugen | 2-core geheugen met 3 GB |

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Aangepaste tekst-naar-spraak | 1 core, 2 GB geheugen | 2-core geheugen met 3 GB |

***

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Kern en geheugen `--cpus` komen `--memory` overeen met de en `docker run` instellingen, die worden gebruikt als onderdeel van de opdracht.

> [!NOTE]
> Het minimum en de aanbevolen zijn gebaseerd op dockerlimieten, *niet* de resources voor de hostmachine. Bijvoorbeeld, spraak-naar-tekst containers geheugen kaart delen van een groot taalmodel, en het *wordt aanbevolen* dat het hele bestand past in het geheugen, dat is een extra 4-6 GB. Ook kan de eerste run van een van beide containers langer duren, omdat modellen worden opgepiept in het geheugen.

## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding biju halen`docker pull`

Containerafbeeldingen voor Spraak zijn beschikbaar in het volgende containerregister.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

| Container | Opslagplaats |
|-----------|------------|
| Spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

| Container | Opslagplaats |
|-----------|------------|
| Aangepaste spraak-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

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

### <a name="docker-pull-for-the-speech-containers"></a>Docker pull voor de Speech containers

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Koppeling van docker voor de spraak-naar-tekstcontainer

Gebruik de [opdracht Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit het containervoorbeeldregister.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> De `latest` tag `en-US` trekt de landint. Zie [Spraak-naar-tekst-landalsvoor](#speech-to-text-locales)extra landplaatsen .

#### <a name="speech-to-text-locales"></a>Spraak-naar-tekstlandines

Alle tags, `latest` behalve in de volgende indeling en zijn hoofdlettergevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

De volgende tag is een voorbeeld van de indeling:

```
2.1.1-amd64-en-us-preview
```

Zie [Spraak-naar-tekst-afbeeldingstags](../containers/container-image-tags.md#speech-to-text)voor alle ondersteunde landlocaties van de **spraak-naar-tekstcontainer.**

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Koppeling van docker voor de aangepaste spraak-naar-tekstcontainer

Gebruik de [opdracht Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit het containervoorbeeldregister.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> De `locale` `voice` en voor aangepaste Spraakcontainers wordt bepaald door het aangepaste model dat door de container wordt ingenomen.

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Koppeling van docker voor de tekst-naar-spraakcontainer

Gebruik de [opdracht Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit het containervoorbeeldregister.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> De `latest` tag `en-US` trekt de `jessarus` land- en stem. Zie [Tekst-naar-spraak-landintalen](#text-to-speech-locales)voor extra landlocaties .

#### <a name="text-to-speech-locales"></a>Tekst-naar-spraakland

Alle tags, `latest` behalve in de volgende indeling en zijn hoofdlettergevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

De volgende tag is een voorbeeld van de indeling:

```
1.3.0-amd64-en-us-jessarus-preview
```

Zie [Tekst-naar-spraak-afbeeldingstags](../containers/container-image-tags.md#text-to-speech)voor alle ondersteunde landlocaties en bijbehorende stemmen van de **tekst-naar-spraak-container.**

> [!IMPORTANT]
> Bij het maken van een *HTTP POST (Standard Text-to-speech)* vereist het `voice` [SSML-bericht (Speech Synthesis Markup Language)](speech-synthesis-markup.md) een element met een `name` kenmerk. De waarde is de bijbehorende containerland- en stem, ook wel de ["korte naam" genoemd.](language-support.md#standard-voices) De `latest` tag heeft bijvoorbeeld een stemnaam van `en-US-JessaRUS`.

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Koppeling van docker voor de aangepaste tekst-naar-spraakcontainer

Gebruik de [opdracht Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit het containervoorbeeldregister.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> De `locale` `voice` en voor aangepaste Spraakcontainers wordt bepaald door het aangepaste model dat door de container wordt ingenomen.

***

## <a name="how-to-use-the-container"></a>Hoe de container te gebruiken

Zodra de container zich op de [hostcomputer bevindt,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

1. [Voer de container](#run-the-container-with-docker-run)uit met de vereiste factureringsinstellingen. Meer [voorbeelden](speech-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.
1. [Query the container prediction endpoint](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met`docker run`

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg [het verzamelen van vereiste parameters](#gathering-required-parameters) `{Endpoint_URI}` voor `{API_Key}` meer informatie over hoe u de en waarden krijgen. Aanvullende [voorbeelden](speech-container-configuration.md#example-docker-run-commands) van `docker run` de opdracht zijn ook beschikbaar.

# <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

Als u de *spraak-naar-tekst-container* wilt uitvoeren, voert u de volgende `docker run` opdracht uit.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Met een *spraak-naar-tekstcontainer wordt een spraak-naar-tekstcontainer* uitgevoerd vanuit de containerafbeelding.
* Hiermee worden 4 CPU-cores en 4 gigabyte (GB) geheugen toegewezen.
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container.
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

De *aangepaste spraak-naar-tekstcontainer* is gebaseerd op een aangepast spraakmodel. Het aangepaste model moet zijn [getraind](how-to-custom-speech-train-model.md) met behulp van de [aangepaste spraakportal.](https://speech.microsoft.com/customspeech)

> [!IMPORTANT]
> Het custom speech-model moet worden getraind vanuit een van de volgende modelversies:
> * **20181201 (v3.3 Unified)**
> * **20190520 (v4.14 Unified)**
> * **20190701 (v4.17 Unified)**<br>
> ![Aangepast containermodel voor spraaktreinen](media/custom-speech/custom-speech-train-model-container-scoped.png)

De aangepaste **spraakmodel-ID** is vereist om de container uit te voeren. Het is te vinden op de **training** pagina van de aangepaste spraak portal. Navigeer in de aangepaste spraakportal naar de **pagina Training** en selecteer het model.
<br>

![Aangepaste pagina voor het trainen van spraak](media/custom-speech/custom-speech-model-training.png)

Verkrijg de **model-id** die `ModelId` u wilt `docker run` gebruiken als argument voor de parameter van de opdracht.
<br>

![Details van het aangepaste spraakmodel](media/custom-speech/custom-speech-model-details.png)

De volgende tabel `docker run` bevat de verschillende parameters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | De [hostcomputer volume mount](https://docs.docker.com/storage/volumes/), die docker gebruikt om het aangepaste model voort te zetten. *C:\CustomSpeech* waarbij het *C-station* zich op de hostmachine bevindt. |
| `{MODEL_ID}` | De aangepaste **spraakmodel-id** van de **trainingspagina** van de aangepaste spraakportal. |
| `{ENDPOINT_URI}` | Het eindpunt is vereist voor meting en facturering. Zie [het verzamelen van vereiste parameters](#gathering-required-parameters)voor meer informatie. |
| `{API_KEY}` | De API-sleutel is vereist. Zie [het verzamelen van vereiste parameters](#gathering-required-parameters)voor meer informatie. |

Als u de *aangepaste spraak-naar-tekstcontainer wilt* uitvoeren, voert u de volgende `docker run` opdracht uit:

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

* Met een *aangepaste spraak-naar-tekstcontainer* wordt een aangepaste spraak-naar-tekstcontainer uitgevoerd vanuit de containerafbeelding.
* Hiermee worden 4 CPU-cores en 4 gigabyte (GB) geheugen toegewezen.
* Hiermee laadt u het aangepaste *spraak-naar-tekstmodel* op de volumeinvoerberg, bijvoorbeeld *C:\CustomSpeech*.
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container.
* Downloadt het `ModelId` model gezien de (indien niet gevonden op het volume mount).
* Als het aangepaste model eerder `ModelId` is gedownload, wordt het model genegeerd.
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

# <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tts)

Als u de container *Tekst naar* spraak `docker run` wilt uitvoeren, voert u de volgende opdracht uit.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *tekst-naar-spraak-container* uit de containerafbeelding.
* Hiermee worden 2 CPU-cores en één gigabyte (GB) geheugen toegewezen.
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container.
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

# <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

De *aangepaste tekst-naar-spraakcontainer* is gebaseerd op een aangepast spraakmodel. Het aangepaste model moet zijn [getraind](how-to-custom-voice-create-voice.md) met behulp van de [aangepaste voice portal.](https://aka.ms/custom-voice-portal) De aangepaste **spraakmodel-ID** is vereist om de container uit te voeren. Het is te vinden op de **training** pagina van de aangepaste voice portal. Navigeer in de aangepaste spraakportal naar de **pagina Training** en selecteer het model.
<br>

![Aangepaste pagina voor spraaktraining](media/custom-voice/custom-voice-model-training.png)

Verkrijg de **model-id** die `ModelId` u wilt gebruiken als argument voor de parameter van de opdracht Docker run.
<br>

![Aangepaste details van het spraakmodel](media/custom-voice/custom-voice-model-details.png)

De volgende tabel `docker run` bevat de verschillende parameters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | De [hostcomputer volume mount](https://docs.docker.com/storage/volumes/), die docker gebruikt om het aangepaste model voort te zetten. *C:\CustomSpeech* waarbij het *C-station* zich op de hostmachine bevindt. |
| `{MODEL_ID}` | De aangepaste **spraakmodel-id** van de **trainingspagina** van de aangepaste spraakportal. |
| `{ENDPOINT_URI}` | Het eindpunt is vereist voor meting en facturering. Zie [het verzamelen van vereiste parameters](#gathering-required-parameters)voor meer informatie. |
| `{API_KEY}` | De API-sleutel is vereist. Zie [het verzamelen van vereiste parameters](#gathering-required-parameters)voor meer informatie. |

Als u de *container Aangepaste tekst naar spraak wilt* uitvoeren, voert u de volgende `docker run` opdracht uit:

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

* Met een *aangepaste tekst-naar-spraakcontainer* wordt een aangepaste tekst-naar-spraakcontainer uitgevoerd vanuit de containerafbeelding.
* Hiermee worden 2 CPU-cores en één gigabyte (GB) geheugen toegewezen.
* Hiermee laadt u het *aangepaste tekst-naar-spraakmodel* op de volumeinvoerhouder, bijvoorbeeld *C:\CustomVoice*.
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container.
* Downloadt het `ModelId` model gezien de (indien niet gevonden op het volume mount).
* Als het aangepaste model eerder `ModelId` is gedownload, wordt het model genegeerd.
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

***

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet.  Zie [Facturering voor](#billing)meer informatie.

## <a name="query-the-containers-prediction-endpoint"></a>Het voorspellingseindpunt van de container opvragen

| Containers | URL van SDK-host | Protocol |
|--|--|--|
| Spraak-naar-tekst en aangepaste spraak-naar-tekst | `ws://localhost:5000` | WS |
| Tekst-naar-spraak en aangepaste tekst-naar-spraak | `http://localhost:5000` | HTTP |

Zie [containerbeveiliging](../cognitive-services-container-support.md#azure-cognitive-services-container-security)voor meer informatie over het gebruik van WSS- en HTTPS-protocollen.

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Tekst-naar-spraak of aangepaste tekst-naar-spraak

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Meerdere containers op dezelfde host uitvoeren

Als u van plan bent om meerdere containers met blootgestelde poorten uit te voeren, zorg ervoor dat elke container met een andere blootgestelde poort wordt uitgevoerd. Voer bijvoorbeeld de eerste container uit op poort 5000 en de tweede container op poort 5001.

U deze container en een andere Azure Cognitive Services-container samen laten uitvoeren op de HOST. U ook meerdere containers van dezelfde Cognitive Services-container laten uitvoeren.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Bij het starten of uitvoeren van de container u problemen ondervinden. Gebruik een [uitvoerbevestiging](speech-container-configuration.md#mount-settings) en schakel logboekregistratie in. Hierdoor kan de container logboekbestanden genereren die handig zijn bij het oplossen van problemen.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De spraakcontainers verzenden factureringsgegevens naar Azure met behulp van een *spraakbron* op uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [Containers configureren](speech-container-configuration.md)voor meer informatie over deze opties.

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het downloaden, installeren en uitvoeren van spraakcontainers. Samenvatting:

* Speech biedt vier Linux-containers voor Docker, waarin verschillende mogelijkheden worden ingekapseld:
  * *Spraak naar tekst*
  * *Aangepaste spraak-naar-tekst*
  * *Tekst naar spraak*
  * *Aangepaste tekst-naar-spraak*
* Containerafbeeldingen worden gedownload uit het containerregister in Azure.
* Containerafbeeldingen worden uitgevoerd in Docker.
* Of u nu de REST API (alleen tekst naar spraak) of de SDK (Spraak-naar-tekst of Tekst-naar-spraak) gebruikt, u geeft de hostURI van de container op. 
* U moet factureringsgegevens verstrekken bij het momentmaken van een container.

> [!IMPORTANT]
>  Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factureringsgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* [Containers configureren](speech-container-configuration.md) voor configuratie-instellingen controleren
* Meer informatie over het [gebruik van Speech-servicecontainers met Kubernetes en Helm](speech-container-howto-on-premises.md)
* Meer [cognitive services-containers gebruiken](../cognitive-services-container-support.md)
