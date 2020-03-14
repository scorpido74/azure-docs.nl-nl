---
title: Containers installeren en uitvoeren-Computer Vision
titleSuffix: Azure Cognitive Services
description: Het downloaden, installeren en uitvoeren van containers voor Computer Vision in deze zelfstudie met stapsgewijze instructies.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 530a8848eceecb1a15d14ce1fef2aa58a3ef5908
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220246"
---
# <a name="install-and-run-read-containers-preview"></a>Lees containers installeren en uitvoeren (preview-versie)

Met containers kunt u de Computer Vision-Api's uitvoeren in uw eigen omgeving. Containers zijn geweldig voor specifieke vereisten voor beveiliging en gegevens beheer. In dit artikel leert u hoe u een Computer Vision-container downloadt, installeert en uitvoert.

Er is een afzonderlijke docker-container, *gelezen*, beschikbaar voor computer vision. De *Lees* container stelt u in staat om *gedrukte tekst* te detecteren en uit te pakken van afbeeldingen van verschillende objecten met verschillende Opper vlakken en achtergronden, zoals bevestigingen, posters en visite kaartjes. Daarnaast detecteert de *Lees* container *handgeschreven tekst* in afbeeldingen en biedt PDF, TIFF en ondersteuning voor meerdere pagina's. Zie de [Lees](concept-recognizing-text.md#read-api) API-documentatie voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u de containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten voor het configureren van de docker-omgeving op [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)en [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Zie het [docker-overzicht](https://docs.docker.com/engine/docker-overview/)voor een primer op basis van docker en container.<br><br> Docker moet worden geconfigureerd, zodat de containers om te verbinden met en facturering gegevens verzenden naar Azure. <br><br> **In Windows**moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker`-opdrachten.| 
|Computer Vision resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure **Computer Vision** -resource en de bijbehorende API-sleutel de EINDPUNT-URI. Beide waarden zijn beschikbaar op de pagina overzicht en sleutels voor de resource en zijn vereist om de container te starten.<br><br>**{API_KEY}** : een van de twee beschik bare bron sleutels op de pagina **sleutels**<br><br>**{ENDPOINT_URI}** : het eind punt op de pagina **overzicht**|

## <a name="request-access-to-the-private-container-registry"></a>Aanvraag voor toegang tot de privécontainerregister

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Ondersteuning voor geavanceerde vector uitbreidingen

De **hostcomputer** is de computer waarop de docker-container wordt uitgevoerd. De host *moet ondersteuning bieden* voor [Geavanceerde vector uitbreidingen](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). U kunt de AVX2-ondersteuning op Linux-hosts controleren met de volgende opdracht:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> De hostcomputer is *vereist* voor de ondersteuning van AVX2. De container werkt *niet* correct zonder ondersteuning voor AVX2.

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie met `docker pull` ophalen

Er zijn container installatie kopieën voor lezen beschikbaar.

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|-----------|------------|
| Lezen | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Gebruik de opdracht [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) om een container installatie kopie te downloaden.

### <a name="docker-pull-for-the-read-container"></a>Docker-pull voor de Lees container

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](computer-vision-resource-container-config.md) van de `docker run`-opdracht beschikbaar. 
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op. 

## <a name="run-the-container-with-docker-run"></a>De container met `docker run` uitvoeren

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het ophalen van de `{ENDPOINT_URI}` en `{API_KEY}` waarden.

[Voor beelden](computer-vision-resource-container-config.md#example-docker-run-commands) van de opdracht `docker run` zijn beschikbaar.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Deze opdracht:

* Voert de Lees container uit van de container installatie kopie.
* Wijst 8 CPU-kernen en 16 GB aan geheugen toe.
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY voor de container toe.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.

Er zijn meer [voor beelden](./computer-vision-resource-container-config.md#example-docker-run-commands) van de `docker run`-opdracht beschikbaar. 

> [!IMPORTANT]
> De opties `Eula`, `Billing`en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](#billing)voor meer informatie.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Query uitvoeren op het prediction-eind punt van de container

De container bevat op REST gebaseerde query Voorspellings eindpunt-Api's. 

Gebruik de host, `http://localhost:5000`voor container-Api's.

### <a name="asynchronous-read"></a>Asynchroon lezen

U kunt de `POST /vision/v2.0/read/core/asyncBatchAnalyze`-en `GET /vision/v2.0/read/operations/{operationId}`-bewerkingen in concert gebruiken om asynchroon een installatie kopie te lezen, vergelijkbaar met de manier waarop de Computer Vision-service die bijbehorende REST-bewerkingen gebruikt. De asynchrone POST-methode retourneert een `operationId` die wordt gebruikt als id voor de HTTP GET-aanvraag.

Selecteer in de Swagger-gebruikers interface de `asyncBatchAnalyze` om deze uit te vouwen in de browser. Selecteer de optie **try-out** > **bestand kiezen**. In dit voor beeld gebruiken we de volgende afbeelding:

![tabbladen versus spaties](media/tabs-vs-spaces.png)

Wanneer de asynchrone POST met succes is uitgevoerd, wordt een **HTTP 202-** status code geretourneerd. Als onderdeel van de reactie bevindt zich een `operation-location`-header die het eind punt van de aanvraag bevat.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

De `operation-location` is de volledig gekwalificeerde URL en is toegankelijk via een HTTP GET. Hier volgt het JSON-antwoord van het uitvoeren van de `operation-location` URL uit de vorige afbeelding:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Synchrone Lees bewerking

U kunt de bewerking `POST /vision/v2.0/read/core/Analyze` gebruiken om een installatie kopie synchroon te lezen. Wanneer de afbeelding volledig is gelezen en vervolgens alleen de API retourneert een JSON-antwoord. De enige uitzonde ring hierop is als er een fout optreedt. Als er een fout optreedt, wordt de volgende JSON geretourneerd:

```json
{
    status: "Failed"
}
```

Het JSON-antwoord object heeft dezelfde object grafiek als de asynchrone versie. Als u een Java script-gebruiker bent en type veiligheid wilt, kan de volgende typen worden gebruikt voor het casten van het JSON-antwoord als een `AnalyzeResult`-object.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Voor een voor beeld van een use-case raadpleegt u de <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">type script sandbox hier <span class="docon docon-navigate-external x-hidden-focus"></span> </a> en selecteert u **uitvoeren** om het gebruiks gemak te visualiseren.

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](./computer-vision-resource-container-config.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturering

De Cognitive Services-containers verzenden facturerings gegevens naar Azure, met behulp van de bijbehorende resource in uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [containers configureren](./computer-vision-resource-container-config.md)voor meer informatie over deze opties.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u geleerd concepten en werkstroom voor het downloaden, installeren en Computer Vision-containers uitvoeren. Samenvatting:

* Computer Vision biedt een Linux-container voor docker, Inge kapseld voor lezen.
* Container installatie kopieën worden gedownload uit het container register container preview in Azure.
* Containerinstallatiekopieën uitvoeren in Docker.
* U kunt de REST API of SDK gebruiken voor het aanroepen van bewerkingen in containers lezen door de URI van de host van de container op te geven.
* Bij het instantiëren van een container, moet u informatie over facturering opgeven.

> [!IMPORTANT]
> Cognitive Services-containers zijn geen licentie om uit te voeren zonder verbinding met Azure voor het meten. Klanten moeten de containers om te communiceren factureringsgegevens met de softwarelicentiecontrole-service te allen tijde inschakelen. Cognitive Services containers verzenden geen klant gegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar micro soft.

## <a name="next-steps"></a>Volgende stappen

* Containers voor configuratie-instellingen [configureren](computer-vision-resource-container-config.md) controleren
* Bekijk [Computer Vision overzicht](Home.md) voor meer informatie over het herkennen van gedrukte en handgeschreven tekst
* Raadpleeg de [Computer Vision-API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) voor meer informatie over de methoden die door de container worden ondersteund.
* Raadpleeg Veelgestelde [vragen (FAQ)](FAQ.md) voor het oplossen van problemen met betrekking tot de functionaliteit van computer vision.
* Meer [Cognitive Services containers](../cognitive-services-container-support.md) gebruiken
